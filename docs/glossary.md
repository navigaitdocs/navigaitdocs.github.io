# Glossary

Definitions of terms, acronyms, and technologies used throughout this documentation.

---

**ADL (Activities of Daily Living)**  
Tasks routinely performed in daily life, such as walking, sitting, and reaching. The Navi robot uses its vision pipeline to detect and analyze ADL patterns for rehabilitation monitoring.

---

**AGX Orin**  
NVIDIA's high-performance embedded AI computing board. In the Navi robot, this board (at `192.168.8.2`) handles all AI inference workloads including the conversational pipeline, LLM inference, ASR/TTS, and vision models.

---

**ALSA (Advanced Linux Sound Architecture)**  
The Linux kernel component that provides audio device drivers. Used as the underlying hardware layer beneath PulseAudio on the robot.

---

**ASR (Automatic Speech Recognition)**  
The process of converting spoken audio to text. On Navi, ASR is handled by NVIDIA Riva running locally on the AGX Orin.

---

**Azure IoT Hub**  
Microsoft's cloud-based IoT message broker. The Navi robot uses it for sending telemetry (battery status, hardware events) to the cloud backend and receiving remote commands.

---

**chatbot.service**  
The primary systemd service on the AGX Orin that runs the Navi conversational AI agent, including the LangGraph dialogue pipeline and Neo4j memory integration.

---

**diff_drive_controller**  
A standard ROS controller that converts linear and angular velocity commands (`/cmd_vel`) into individual left and right wheel speed commands for a differential drive robot.

---

**ESP32**  
A low-power wireless microcontroller inside the Navi robot responsible for power management, head/neck servo control, and digital I/O.

---

**GStreamer**  
An open-source multimedia pipeline framework. Used on Navi to capture microphone audio from hardware and broadcast it over UDP to downstream consumers (ASR, VAD, wake word detection).

---

**Hotspot**  
The Wi-Fi access point broadcast by the Jetson Orin NX (`hotspot.service`). Used for development SSH access and ROS communication during onboarding.

---

**IoT Hub**  
See *Azure IoT Hub*.

---

**Jetson**  
NVIDIA's family of embedded computing modules for AI at the edge. The Navi robot uses two Jetson boards: the Orin NX (ROS control) and the AGX Orin (AI compute).

---

**LangGraph**  
A Python library for building stateful, multi-step AI agent workflows as graphs. Used in Navi's `chatbot.service` to manage the conversational dialogue pipeline.

---

**LLM (Large Language Model)**  
An AI model trained on large text datasets capable of generating human-like responses. Navi uses both cloud-hosted LLMs and edge-optimized models via the `nano_llm_tools.service`.

---

**Neo4j**  
A graph database used by Navi's conversational pipeline as a hybrid RAG (Retrieval-Augmented Generation) long-term memory store for personalized, context-aware conversations.

---

**Noetic**  
The final LTS (Long-Term Support) release of ROS 1, targeting Ubuntu 20.04. The Navi robot runs ROS 1 Noetic.

---

**Orin NX**  
NVIDIA Jetson Orin NX — the ROS controller board at `192.168.8.1`. Acts as the ROS Master and manages navigation, motor control, sensor fusion, and the Wi-Fi hotspot.

---

**PulseAudio**  
A Linux sound server that sits between applications and the ALSA hardware layer. Used on Navi for application-level volume control via `pactl` commands.

---

**RAG (Retrieval-Augmented Generation)**  
An AI technique that combines a retrieval system (fetching relevant stored data) with generative model output. Navi uses a hybrid RAG system backed by Neo4j for long-term user memory.

---

**RIVA**  
NVIDIA Riva — a GPU-accelerated speech AI SDK providing real-time on-device ASR (speech-to-text) and TTS (text-to-speech). Runs in a container via `riva_container.service`.

---

**ROSSerial**  
A ROS package that enables communication between a ROS network and microcontrollers (like the STM32) over a serial (USB) connection. Bridges ROS topics to/from the STM32.

---

**ROS Master**  
The central ROS node (`roscore`) that all other nodes register with to discover each other. On Navi, the ROS Master runs on the Orin NX at `192.168.8.1:11311`.

---

**RS485**  
A serial communication standard used for robust, long-distance communication between the STM32 microcontroller and the robot's motor driver.

---

**SLAM (Simultaneous Localization and Mapping)**  
A technique allowing a robot to build a map of an unknown environment while simultaneously tracking its location within that map. Used by Navi's SlamKit module with the RealSense camera.

---

**SlamKit**  
The navigation and mapping module on the Navi robot, integrated with the Intel RealSense camera for SLAM-based navigation.

---

**STM32**  
STMicroelectronics 32-bit ARM microcontroller. In Navi, the STM32 acts as the low-level hardware interface: driving motors via RS485, reading cliff sensors and bumpers, and controlling RGB LEDs.

---

**systemd**  
The Linux init system and service manager. Used on both Navi boards to define, start, and monitor all robot processes as `.service` units.

---

**TTS (Text-to-Speech)**  
The process of converting text to spoken audio. On Navi, TTS is handled either by NVIDIA Riva (on-device) or ElevenLabs (cloud).

---

**VAD (Voice Activity Detection)**  
An algorithm that detects when a user is speaking (as opposed to background noise or silence). Used in Navi's audio pipeline to determine when to start/stop ASR processing.

---

**YAML**  
A human-readable data format used for configuration files. The `diff_drive_controller` reads its configuration from a YAML file on the ROS parameter server.

---

**YOLOv8**  
A real-time object detection deep learning model. Used in Navi's ADL pipeline for person detection via the Orbbec depth camera.
