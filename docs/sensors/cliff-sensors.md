# Cliff Sensors

## Overview

The Navi robot uses **analog cliff detection sensors** to prevent it from falling off edges such as stairs, ramps, or elevated platforms. These sensors are wired to the STM32 microcontroller, which publishes the data to the ROS network.

## Topic

| Field | Value |
|---|---|
| **Topic** | `/cliff_sensor` |
| **Message Type** | `custom_msg` |
| **Direction** | Publish (STM32 → ROS) |

## Monitoring Cliff Data

```bash
rostopic echo /cliff_sensor
```

```bash
# Check publish rate
rostopic hz /cliff_sensor
```

## Hardware Details

- Sensors are **analog** and connected directly to the STM32
- The STM32 reads analog voltage levels to detect a drop-off
- Data is bridged to ROS via the **ROSSerial** communication layer

## Integration Notes

- You can subscribe to `/cliff_sensor` in your application to trigger emergency stops or alerts
- Cliff detection events can be combined with `/bumper_state` for a complete collision/safety picture
- The cliff sensor data is also consumed by the `rehab_boot.service` navigation stack for automatic obstacle avoidance
