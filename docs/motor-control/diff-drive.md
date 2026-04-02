# Differential Drive

## Overview

The Navi robot uses a **differential drive** configuration with two motorized wheels. The control chain flows from the ROS navigation stack through multiple layers down to the physical motor hardware.

## Control Chain

```
ROS Navigation Stack
        │
        │  publishes /cmd_vel (geometry_msgs/Twist)
        ▼
Diff_Drive Controller
        │
        │  converts to /wheel_cmd_velocities
        ▼
Hardware Interface
        │
        │  Read() / Write() loop
        ▼
ROSSerial Bridge
        │
        │  serial communication
        ▼
STM32 Microcontroller
        │
        │  RS485
        ▼
Motor Driver → Wheels
```

## Controller Manager

The Controller Manager loads the `diff_drive_controller` from a YAML configuration file. The controller reads URDF parameters from the ROS parameter server:

- Joint names
- Wheel radius
- Maximum velocity

## Hardware Interface

The Hardware Interface has two core functions that run in a continuous loop:

| Function | Description |
|---|---|
| `Read()` | Subscribes to `/measured_joint_states` and updates `joint_positions_` and `joint_velocities_` |
| `Write()` | Publishes velocity commands as `rehab_msgs::WheelsCmdStamped` on `/wheel_cmd_velocities` |

## Sensor Feedback

The STM32 provides continuous feedback to the ROS network:

| Topic | Type | Description |
|---|---|---|
| `/measured_joint_states` | `sensor_msgs/JointState` | Wheel encoder positions and velocities |
| `/cliff_sensor` | `custom_msg` | Cliff/edge detection |
| `/bumper_state` | `custom_msg` | Physical bumper contact events |

## ROSSerial Bridge

ROSSerial bridges the ROS network with the STM32 over a serial connection. It handles message serialization/deserialization between the ROS environment and the microcontroller firmware.

## Velocity Commands

The standard `/cmd_vel` topic uses `geometry_msgs/Twist`:

```bash
# Move forward
rostopic pub /cmd_vel geometry_msgs/Twist \
  "linear: {x: 0.2, y: 0.0, z: 0.0}
   angular: {x: 0.0, y: 0.0, z: 0.0}" --once

# Rotate in place
rostopic pub /cmd_vel geometry_msgs/Twist \
  "linear: {x: 0.0, y: 0.0, z: 0.0}
   angular: {x: 0.0, y: 0.0, z: 0.5}" --once

# Stop
rostopic pub /cmd_vel geometry_msgs/Twist \
  "linear: {x: 0.0, y: 0.0, z: 0.0}
   angular: {x: 0.0, y: 0.0, z: 0.0}" --once
```
