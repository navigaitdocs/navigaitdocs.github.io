# Volume Control — ROS Interface

## Overview

ROS-based volume control topics provide programmatic control of the robot's audio hardware from within the ROS ecosystem. These topics interface directly with the **hardware amplifier**.

!!! danger "Hardware Amplifier Control — Internal Use Only"
    The ROS audio topics control the hardware amplifier level **directly**. This interface is reserved for NaviGait's internal use only and should **never** be used at the application level. Incorrect amplifier settings can damage speaker hardware.

    **For application-level volume control, use the [PulseAudio commands](volume-linux.md) instead.**

## Topics

| Topic |
|---|
| `/audio_mute_control` |
| `/audio_volume_control` | 
| `/audio_power_control` | 

## When to Use Each Interface

| Scenario | Use |
|---|---|
| Application adjusting playback volume | [PulseAudio (Linux)](volume-linux.md) |
| NaviGait internal amplifier calibration | ROS topics (this page) |
| Muting microphone input | `pactl set-source-mute @DEFAULT_SOURCE@ toggle` |
