# Changelog

All notable changes to this documentation are tracked here.

---

## [1.0] — April 2026

### Initial Release

This is the first public release of the Navi Robot Hardware Interface Guide for third-party integration partners.

**Coverage in this release:**

- Full network architecture documentation (internal topology, external Wi-Fi, cloud connectivity)
- SSH access guide for both Orin NX and AGX Orin boards
- Complete ROS 1 (Noetic) topic and parameter reference
- Navigation control (`/module_status`, `/cmd_vel`)
- Head/neck control (`/neck_cmd`)
- Facial expression control (`/smiley_param`)
- Camera systems (Orbbec depth, Intel RealSense)
- Cliff sensor and bumper sensor documentation
- Battery monitoring (ROS topic and Azure IoT Hub)
- Audio system documentation (GStreamer microphone, Cloud TTS, Edge TTS)
- PulseAudio volume control reference (full command set)
- ROS audio topic reference (amplifier-level, internal use only)
- Systemd service documentation for all 11 services across both boards
- Boot sequence documentation with phase diagrams
- Differential drive controller and motor control chain
- RGB LED control
- Integration guide for replacing the conversational AI pipeline
- Companion app integration reference
- Troubleshooting guide
- Glossary of all key terms and technologies
- Quick reference cheat sheet

---

## Upcoming

The following topics are planned for future documentation releases:

- SlamKit configuration and SLAM map management
- URDF robot model reference
- ROS parameter server full listing
- Neo4j memory system schema (if made available to partners)
- OTA update process
- Multi-robot deployment considerations
- Power management and battery charging specifications

---

!!! info "Feedback"
    To report errors, request additional coverage, or provide feedback on this documentation, contact the Navigait integration team.
