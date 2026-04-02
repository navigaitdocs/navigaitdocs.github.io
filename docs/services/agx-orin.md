# AGX Orin Services

The Jetson AGX Orin is the **AI Compute** board. It runs all services related to the conversational AI pipeline, speech, and cloud communication.

## chatbot.service

| Field | Detail |
|---|---|
| **Purpose** | Runs the Navi conversational AI agent — the central intelligence module |
| **Dependencies** | `nano_llm_tools.service`, `riva_container.service`, `audio-broadcaster.service`, `video-recorder.service` |
| **Restart Policy** | Always (auto-restart on failure) |
| **Start Order** | Phase 3 — after all upstream dependencies are healthy |

This service manages:

- LangGraph-based dialogue pipeline
- User interactions via speech
- LLM query processing
- Neo4j hybrid RAG long-term memory for personalized conversations

```bash
systemctl status chatbot.service
journalctl -u chatbot.service -f
```

Logs are written to `~/chatbot_service_output.log` and `~/chatbot_service_error.log`.

## audio-broadcaster.service

| Field | Detail |
|---|---|
| **Purpose** | Activates GStreamer pipelines to capture and broadcast microphone streams |
| **Dependencies** | System audio hardware (ALSA/PulseAudio) |
| **Restart Policy** | Always |
| **Start Order** | Phase 1 (early boot) — required before `chatbot.service` |

Makes raw audio input available to ASR, wake word detection, and voice activity detection modules over GStreamer's UDP streaming infrastructure.

```bash
systemctl status audio-broadcaster.service
journalctl -u audio-broadcaster.service -f
```

## riva_container.service

| Field | Detail |
|---|---|
| **Purpose** | Launches the NVIDIA Riva container for on-device ASR and TTS |
| **Dependencies** | NVIDIA Container Runtime, GPU drivers |
| **Restart Policy** | Always |
| **Start Order** | Phase 1 (early boot) — must be healthy before `chatbot.service` starts |

Provides real-time speech-to-text and text-to-speech processing locally on the Jetson hardware, eliminating the need for cloud-based speech services and ensuring low-latency voice interaction.

```bash
systemctl status riva_container.service
journalctl -u riva_container.service -f
```

## nano_llm_tools.service

| Field | Detail |
|---|---|
| **Purpose** | Starts the Nano LLM container for edge-optimized LLM inference |
| **Dependencies** | NVIDIA Container Runtime, GPU drivers |
| **Restart Policy** | Always |
| **Start Order** | Phase 1 (early boot) — must be healthy before `chatbot.service` starts |

Hosts lightweight, edge-optimized large language model tools on the Jetson platform, providing core LLM inference capabilities for the conversational agent.

```bash
systemctl status nano_llm_tools.service
journalctl -u nano_llm_tools.service -f
```

## iothub_monitor.service

| Field | Detail |
|---|---|
| **Purpose** | Bidirectional IoT Hub communication between the robot and cloud backend |
| **Dependencies** | Network connectivity, Azure IoT Hub credentials |
| **Restart Policy** | Always |
| **Start Order** | Phase 2 — after network is available |

Relays commands, configuration updates, telemetry data, and status messages, ensuring the robot stays synchronized with the backend management platform.

```bash
systemctl status iothub_monitor.service
journalctl -u iothub_monitor.service -f
```

## video-recorder.service

| Field | Detail |
|---|---|
| **Purpose** | Activates camera hardware and manages video recording sessions |
| **Dependencies** | Camera hardware (CSI/USB), ffmpeg |
| **Restart Policy** | Always |
| **Start Order** | Phase 1 — independent of conversational pipeline |

Initializes the camera feed for activity recognition, gait analysis, and fall detection. Handles on-demand video capture, encoding via ffmpeg, and local storage.

```bash
systemctl status video-recorder.service
journalctl -u video-recorder.service -f
```

Logs: `~/camera-service_output.log` and `~/camera-service_error.log`.

## robot-sync-api.service

| Field | Detail |
|---|---|
| **Purpose** | Fetches the latest application configuration from the cloud backend at boot |
| **Dependencies** | Network connectivity, backend API availability |
| **Restart Policy** | No (oneshot — runs only once at boot) |
| **Start Order** | Phase 2 (early) — before `chatbot.service` |

Syncs model modes, persona settings, and other configurable parameters updated via the companion mobile app while the robot was offline. Ensures the robot always starts with the most current configuration.

```bash
systemctl status robot-sync-api.service
journalctl -u robot-sync-api.service
```

## .bashrc Configuration (AGX Orin)

Every new terminal on the AGX Orin automatically:

1. Sets device identification by exporting the device/robot name
2. Initializes Conda environment
3. Sources ROS workspaces (`/opt/ros/noetic/setup.bash` and `~/ros_ws/devel/setup.bash`)
4. Enables GUI applications (X11 Display)
5. Fixes OpenMP shared library issues
6. Sets ROS networking variables: `ROS_MASTER_URI`, `ROS_HOSTNAME`, `ROS_IP`
7. Exports device MAC address
