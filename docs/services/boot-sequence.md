# Boot Sequence

## Overview

Services start in a defined order across **three phases** to satisfy inter-service dependencies. The sequence ensures GPU containers and hardware are ready before the conversational AI agent starts.

## Boot Phases

```
PHASE 1 — Hardware & Containers
┌─────────────────────────────────────────────────────────────┐
│  riva_container.service       (GPU: ASR/TTS)                │
│  nano_llm_tools.service       (GPU: LLM inference)          │
│  audio-broadcaster.service    (Microphone / GStreamer)       │
│  video-recorder.service       (Camera / ffmpeg)             │
└─────────────────────────────────────────────────────────────┘
                          │
                          ▼
PHASE 2 — Network & Sync
┌─────────────────────────────────────────────────────────────┐
│  robot-sync-api.service       (oneshot: fetch cloud config) │
│  iothub_monitor.service       (IoT Hub monitoring begins)   │
└─────────────────────────────────────────────────────────────┘
                          │
                          ▼
PHASE 3 — Core Intelligence
┌─────────────────────────────────────────────────────────────┐
│  chatbot.service              (Conversational AI agent)      │
└─────────────────────────────────────────────────────────────┘
```

## Phase Detail

### Phase 1 — Hardware & Containers

| Service | Board | Notes |
|---|---|---|
| `riva_container.service` | AGX Orin | GPU container for ASR/TTS — must be healthy before chatbot |
| `nano_llm_tools.service` | AGX Orin | GPU container for LLM inference — must be healthy before chatbot |
| `audio-broadcaster.service` | AGX Orin | GStreamer microphone pipeline — required before chatbot |
| `video-recorder.service` | AGX Orin | Camera hardware initialization — independent of conversational pipeline |

### Phase 2 — Network & Sync

| Service | Board | Notes |
|---|---|---|
| `robot-sync-api.service` | AGX Orin | Oneshot — fetches latest cloud config (persona, model modes, etc.) |
| `iothub_monitor.service` | AGX Orin | Starts after network is available; runs continuously |

### Phase 3 — Core Intelligence

| Service | Board | Notes |
|---|---|---|
| `chatbot.service` | AGX Orin | Starts only after all Phase 1 & 2 dependencies are healthy |

## Orin NX Boot (Parallel)

The Orin NX runs its own independent boot sequence in parallel:

| Service | Notes |
|---|---|
| `hotspot.service` | Starts first — enables Wi-Fi hotspot for dev access |
| `rehab_boot.service` | Launches ROS master and navigation stack |
| `cloud_comm.service` | Starts battery telemetry to Azure IoT Hub |
| `orbbec_camera.service` | Starts Orbbec camera ROS node |

## Checking Boot Status

```bash
# Check all service statuses at once
systemctl status hotspot.service rehab_boot.service cloud_comm.service

# On AGX Orin — check AI services
systemctl status chatbot.service riva_container.service nano_llm_tools.service

# View full boot log
journalctl -b
```

!!! warning "Service Ordering is Critical"
    `chatbot.service` depends on `riva_container.service`, `nano_llm_tools.service`, `audio-broadcaster.service`, and `video-recorder.service` being active. If any dependency fails, the chatbot will not start correctly.
