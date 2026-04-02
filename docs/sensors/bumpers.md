# Bumpers

## Overview

Physical contact bumpers are wired to the STM32 microcontroller and publish collision events to the ROS network. Bumper activation can be used to trigger emergency stop behavior or alert logic in your application.

## Topic

| Field | Value |
|---|---|
| **Topic** | `/bumper_state` |
| **Message Type** | `custom_msg` |
| **Direction** | Publish (STM32 → ROS) |

## Monitoring Bumper State

```bash
rostopic echo /bumper_state
```

## Integration Notes

- Subscribe to `/bumper_state` to receive real-time contact events
- Use bumper events to trigger emergency stops in custom navigation pipelines
- Bumper data flows from the STM32 through ROSSerial to the Orin NX
