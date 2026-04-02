# Volume Control — ROS Interface

## Overview

ROS-based volume control topics provide programmatic control of the robot's audio hardware from within the ROS ecosystem. These topics interface directly with the **hardware amplifier**.

!!! danger "Hardware Amplifier Control — Internal Use Only"
    The ROS audio topics control the hardware amplifier level **directly**. This interface is reserved for NaviGait's internal use only and should **never** be used at the application level. Incorrect amplifier settings can damage speaker hardware.

    **For application-level volume control, use the [PulseAudio commands](volume-linux.md) instead.**

## Topics

| Topic | Message Type | Description |
|---|---|---|
| `/audio_mute_control` | `std_msgs/Bool` | `true` = Mute, `false` = Unmute |
| `/audio_volume_control` | `std_msgs/Int8` | `0` = Min volume, `100` = Max volume |
| `/audio_power_control` | `std_msgs/Bool` | `true` = Power on, `false` = Power off |

## Commands

### Mute

```bash
rostopic pub /audio_mute_control std_msgs/Bool "data: true" --once
```

### Unmute

```bash
rostopic pub /audio_mute_control std_msgs/Bool "data: false" --once
```

### Set Volume Level

```bash
rostopic pub /audio_volume_control std_msgs/Int8 "data: 50" --once
```

Value range: `0` (minimum) to `100` (maximum).

### Power On Amplifier

```bash
rostopic pub /audio_power_control std_msgs/Bool "data: true" --once
```

### Power Off Amplifier

```bash
rostopic pub /audio_power_control std_msgs/Bool "data: false" --once
```

## When to Use Each Interface

| Scenario | Use |
|---|---|
| Application adjusting playback volume | [PulseAudio (Linux)](volume-linux.md) |
| NaviGait internal amplifier calibration | ROS topics (this page) |
| Muting microphone input | `pactl set-source-mute @DEFAULT_SOURCE@ toggle` |
