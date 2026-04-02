# RGB LEDs

## Overview

The Navi robot's RGB LEDs are controlled by the **STM32 microcontroller** via the `/rgb` ROS topic. LEDs are used to communicate robot status visually — such as navigation mode, charging state, and error conditions.

## ROS Topic

| Field | Value |
|---|---|
| **Topic** | `/rgb` |
| **Message Type** | `custom_msg` |
| **Direction** | Subscribe (STM32 receives from ROS) |

## Publishing LED Commands

```bash
# Publish an RGB command to the STM32
rostopic pub /rgb <custom_msg_type> "<your_msg_data>" --once
```

!!! note
    The exact message format for `/rgb` is defined in the `rehab_msgs` custom message package. Refer to the message definition in the ROS workspace for field names and value ranges.

## Common Use Cases

| State | LED Behavior |
|---|---|
| Navigation active | Status color (defined by application) |
| Charging | Charging indicator pattern |
| Error / fault | Alert color pattern |
| Idle | Default/standby color |

## Hardware Path

```
ROS Master (Orin NX)
      │
      │  /rgb topic
      ▼
ROSSerial Bridge
      │
      │  Serial
      ▼
STM32 Microcontroller
      │
      │  Digital control signals
      ▼
RGB LED Array
```

## Monitoring Current LED Commands

```bash
rostopic echo /rgb
```
