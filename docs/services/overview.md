# Services Overview

## Architecture

The Navi robot runs two separate compute boards, each with its own set of systemd services. Services are configured to start automatically on boot in a defined sequence.

| Board | IP | Primary Role |
|---|---|---|
| **Jetson Orin NX** | `192.168.8.1` | ROS control, navigation, sensors |
| **Jetson AGX Orin** | `192.168.8.2` | AI inference, conversational pipeline |

## All Services at a Glance

### Orin NX (ROS Controller Board)

| Service | Description | Restart Policy |
|---|---|---|
| `hotspot.service` | Initializes the Jetson wireless hotspot for remote access | On boot |
| `rehab_boot.service` | Launches robotic and autonomous functionalities (navigation, motor control, sensor drivers) | On boot |
| `cloud_comm.service` | Manages IoT Hub telemetry including battery status payloads | On boot |
| `orbbec_camera.service` | Launches the Orbbec depth camera driver and ROS node | Always |

### AGX Orin (AI Compute Board)

| Service | Description | Restart Policy |
|---|---|---|
| `chatbot.service` | Runs the conversational AI agent (central intelligence module) | Always |
| `audio-broadcaster.service` | GStreamer audio pipelines for microphone capture and broadcast | Always |
| `riva_container.service` | NVIDIA Riva container for on-device ASR and TTS | Always |
| `nano_llm_tools.service` | Edge-optimized LLM inference container | Always |
| `iothub_monitor.service` | Bidirectional IoT Hub communication for commands and telemetry | Always |
| `video-recorder.service` | Camera hardware initialization and video recording management | Always |
| `robot-sync-api.service` | Fetches latest cloud configuration at boot (oneshot) | No (oneshot) |

## Service Management Commands

```bash
# Check service status
systemctl status <service-name>

# View live service logs
journalctl -u <service-name> -f

# Restart a service
sudo systemctl restart <service-name>

# Enable a service (persist across reboots)
sudo systemctl enable <service-name>

# Disable a service (testing only — re-enable before reboot)
sudo systemctl disable <service-name>
```

!!! danger "Critical Warning"
    **Do not modify or disable systemd service configurations.**
    If any service is stopped for testing, ensure it is **re-enabled before the next system reboot**. Failure to do so may result in incomplete system initialization and loss of core functionality.
