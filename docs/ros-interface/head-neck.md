# Head & Neck Control

## Overview

The robot's neck mechanism can be controlled either via joystick input or programmatically by setting the `/neck_cmd` ROS parameter. Control signals are processed by the ESP32 wireless MCU, which drives the head/neck servo.

## ROS Parameter

```bash
rosparam set /neck_cmd <mode_integer>
```

## Mode Reference

| Value | Behavior |
|---|---|
| `1` | Mean / Neutral Position |
| `2` | Mean / Neutral Position |
| `3` | Rotate Left |
| `4` | Rotate Right |

## Examples

### Neutral

```bash
rosparam set /neck_cmd 1
```

### Rotate Left

```bash
rosparam set /neck_cmd 3
```

### Rotate Right

```bash
rosparam set /neck_cmd 4
```

## Verifying the Current Value

```bash
rosparam get /neck_cmd
```

## Hardware Notes

- The head/neck servo is controlled by the **ESP32** via digital signals
- The ESP32 receives commands from the Jetson AGX Orin via digital I/O
- Joystick input (when a joystick dongle is attached) can also drive neck movement and takes precedence over parameter-based control
