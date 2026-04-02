# Navi Robot — Hardware Interface Guide

| Section | Description |
|---|---|
| [Hardware Interface](getting-started/overview.md) | Complete hardware specifications for sensors, actuators, cameras, and compute modules. |
| [ROS Interface](ros-interface/environment.md) | Full ROS 1 (Noetic) topic and parameter reference for navigation, motion, and expressions. |
| [Audio System](audio/microphone.md) | Microphone capture, speaker output, PulseAudio and ROS-based volume control. |
| [System Services](services/overview.md) | Systemd services, boot sequence, and service management across both compute boards. |
| [Troubleshooting](troubleshooting.md) | Step-by-step solutions for connectivity, ROS, audio, camera, and service failures. |
| [Quick Reference](quick-reference.md) | Single-page cheat sheet with every command you need, organized by category. |

---

## Platform Overview

The **Navi Robot** is a conversational rehabilitation and assistance robot built on a dual-board NVIDIA Jetson architecture, running ROS 1 (Noetic). This documentation provides hardware interface specifications for third-party developers building custom applications on the Navi platform.

!!! warning "Confidential"
    This document is intended solely for **authorized integration partners**. The proprietary Navi conversational AI application layer is not covered and is not accessible to integration partners.

---

## Compute Architecture

| Component | Hardware | Primary Role |
|---|---|---|
| **Main Compute** | NVIDIA Jetson AGX Orin | AI inference, conversational pipeline, vision models |
| **ROS Controller** | NVIDIA Jetson Orin NX | ROS master, navigation, motor control, sensor fusion |
| **Microcontroller** | STM32 | Low-level motor driver, cliff sensors, bumpers, RGB LEDs |
| **Wireless MCU** | ESP32 | Power management, head/neck servo control, digital I/O |

---

## Document Scope

This guide covers:

- Network topology and IP addressing for all onboard devices
- ROS topic and parameter interfaces for sensors and actuators
- Camera and microphone hardware access
- Audio output and volume control
- Navigation, head/neck, and expression control interfaces
- System service architecture and boot sequence
- Diagnostic and monitoring utilities

---

## Document Info

| Field | Value |
|---|---|
| **Platform** | NVIDIA Jetson AGX Orin & Jetson Orin NX |
| **ROS Version** | ROS 1 (Noetic) |
| **Document Version** | 1.0 |
| **Date** | April 2026 |
| **Organization** | Navigait (Xavorcorp) |
