# Navigation Control

## Overview

Autonomous navigation on the Navi robot is controlled by publishing to the `/module_status` topic. This toggles the navigation mode on or off without requiring a service restart.

## Topic

| Field | Value |
|---|---|
| **Topic** | `/module_status` |
| **Message Type** | `std_msgs/String` |
| **Direction** | Publish |

## Commands

### Enable Navigation

```bash
rostopic pub /module_status std_msgs/String "data: 'NavMode,1'" --once
```

### Disable Navigation

```bash
rostopic pub /module_status std_msgs/String "data: 'NavMode,0'" --once
```

!!! tip "The `--once` flag"
    The `--once` flag ensures the message is published a single time and the command exits cleanly. Without it, `rostopic pub` will continuously republish the message.

## Velocity Control

For direct manual velocity control, publish to the `/cmd_vel` topic using standard ROS `geometry_msgs/Twist` messages:

```bash
# Move forward at 0.2 m/s
rostopic pub /cmd_vel geometry_msgs/Twist \
  "linear:
    x: 0.2
    y: 0.0
    z: 0.0
  angular:
    x: 0.0
    y: 0.0
    z: 0.0" --once
```

| Field | Description |
|---|---|
| `linear.x` | Forward/backward velocity (m/s) |
| `angular.z` | Rotational velocity (rad/s) |

## Companion App Navigation Topics

The companion Android tablet also publishes navigation commands over the ROS network:

| Topic | Direction | Description |
|---|---|---|
| `/cmd_frm_tablet` | Subscribe | Commands received from the companion app |
| `/module_status` | Subscribe | Module toggle commands (e.g., NavMode) |
| `/control` | Subscribe | General control commands from the app |
| `/cmd_vel` | Subscribe | Manual joystick velocity commands |
