# Platform Overview

The Navi Robot is built on a **dual-board NVIDIA Jetson architecture** with a clear separation of concerns: one board handles ROS-based robot control, and the other handles AI inference and the conversational pipeline.

## Hardware Architecture

```
┌─────────────────────────────────────────────────────┐
│                    NAVI ROBOT                       │
│                                                     │
│  ┌──────────────────┐    ┌──────────────────────┐   │
│  │  Jetson Orin NX  │    │  Jetson AGX Orin     │   │
│  │  (ROS Master)    │◄──►│  (AI Compute)        │   │
│  │  192.168.8.1     │    │  192.168.8.2         │   │
│  └────────┬─────────┘    └──────────────────────┘   │
│           │ Ethernet                                │
│  ┌────────▼──────┐                                  │
│  │ Ethernet      │                                  │
│  │ Switch        │                                  │
│  └───────────────┘                                  │
│           │                                         │
│  ┌────────▼───────┐                                 │
│  │  Robot Screen  │                                 │
│  │  (Display)     │                                 │
│  │  192.168.8.3   │                                 │
│  └────────────────┘                                 │
└─────────────────────────────────────────────────────┘
```

## Compute Boards

### Jetson Orin NX — ROS Controller

The Orin NX is the **ROS Master** and primary robot control board. It manages:

- ROS Master node (`192.168.8.1:11311`)
- Navigation stack and motor control
- Sensor fusion (cliff sensors, bumpers, encoders)
- Wi-Fi hotspot for development access
- Cloud telemetry via `cloud_comm.service`

### Jetson AGX Orin — AI Compute

The AGX Orin is a **ROS Slave** dedicated to AI workloads. It manages:

- Conversational AI pipeline (`chatbot.service`)
- NVIDIA Riva ASR/TTS (on-device speech)
- Edge LLM inference (`nano_llm_tools.service`)
- Video recording and vision models
- IoT Hub bidirectional communication

## Microcontrollers

The **low-level microcontrollers** are handling: 

- Wheel motor drivers 
- Fornt/ Rear Bumpers
- RGB status LEDs
- Communication with ROS via ROSSerial
- Power management
- Head/neck servo control

## Internal IP Map

| Device | IP Address | Role |
|---|---|---|
| Jetson Orin NX | `192.168.8.1` | ROS Master, Hotspot |
| Jetson AGX Orin | `192.168.8.2` | AI Compute (ROS Slave) |
| Robot Screen (Tablet) | `192.168.8.3` | Robot Facial Display|

!!! note
    The `192.168.8.x` subnet is reserved for the robot's internal network. Do not configure conflicting subnets on the home network.
