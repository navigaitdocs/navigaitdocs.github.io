# AGX Orin Services

The Jetson AGX Orin is the **AI Compute** board. It runs all services related to the conversational AI pipeline, speech, and cloud communication.

## chatbot.service

| Field | Detail |
|---|---|
| **Purpose** | Runs the Navi conversational AI agent — the central intelligence module |
| **Dependencies** | `nano_llm_tools.service`, `riva_container.service`, `audio-broadcaster.service`, `video-recorder.service` |
| **Restart Policy** | Always (auto-restart on failure) |
| **Start Order** | Phase 3 — after all upstream dependencies are healthy |

```bash
systemctl status chatbot.service
journalctl -u chatbot.service -f
```

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

## Orbbec Camera Container

The Orbbec depth camera driver runs via a Docker container (`aliahmadxvr/orbbec-noetic:jetpack5.1.2`) directly on boot using `--restart=always`. You can disable it if the camera is not required.

### Manual Run (Interactive/Debugging)

Use this command to run the container in interactive mode. The `--rm` flag ensures the container is automatically removed when stopped.

```bash
sudo docker run -it --rm \
  --name orbbec-container \
  --network=host \
  -e ROS_MASTER_URI=http://192.168.8.1:11311 \
  -e ROS_HOSTNAME=$(hostname -I | awk '{print $1}') \
  -e ROS_IP=$(hostname -I | awk '{print $1}') \
  --runtime nvidia \
  --device /dev/bus/usb:/dev/bus/usb \
  --privileged \
  aliahmadxvr/orbbec-noetic:jetpack5.1.2
```

### Run on Boot (Automatic Startup)

To ensure the camera driver and ROS topics are published automatically on every boot, use the command below. The `--restart=always` flag lets Docker manage automatic startup.

```bash
sudo docker run -d \
  --name orbbec-container \
  --network=host \
  -e ROS_MASTER_URI=http://192.168.8.1:11311 \
  -e ROS_HOSTNAME=$(hostname -I | awk '{print $1}') \
  -e ROS_IP=$(hostname -I | awk '{print $1}') \
  --runtime nvidia \
  --device /dev/bus/usb:/dev/bus/usb \
  --privileged \
  --restart=always \
  aliahmadxvr/orbbec-noetic:jetpack5.1.2
```

