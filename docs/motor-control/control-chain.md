# Control Chain

## Full System Control Flow

The complete motor control architecture from high-level navigation commands to physical motor actuation:

```
┌──────────────────────────────────────┐
│      ROS Navigation Stack            │
│   (move_base / path planning)        │
└──────────────┬───────────────────────┘
               │ /cmd_vel
               │ geometry_msgs/Twist
               ▼
┌──────────────────────────────────────┐
│        Diff_Drive Controller         │
│  (loaded by Controller Manager       │
│   from YAML config file)             │
│                                      │
│  - Reads URDF from parameter server  │
│    (joint names, wheel radius,       │
│     max velocity)                    │
└──────────────┬───────────────────────┘
               │ /wheel_cmd_velocities
               │ rehab_msgs::WheelsCmdStamped
               ▼
┌──────────────────────────────────────┐
│         Hardware Interface           │
│                                      │
│  Read()  ◄── /measured_joint_states  │
│  Write() ──► /wheel_cmd_velocities   │
└──────────────┬───────────────────────┘
               │ Serial (USB)
               ▼
┌──────────────────────────────────────┐
│         ROSSerial Bridge             │
│  (ROS ↔ STM32 serialization layer)  │
└──────────────┬───────────────────────┘
               │ UART / Serial
               ▼
┌──────────────────────────────────────┐
│          STM32 Microcontroller       │
│                                      │
│  - Receives wheel velocity commands  │
│  - Reads encoder feedback            │
│  - Controls cliff sensors & bumpers  │
│  - Controls RGB LEDs                 │
└──────────────┬───────────────────────┘
               │ RS485
               ▼
┌──────────────────────────────────────┐
│           Motor Driver               │
└──────────────┬───────────────────────┘
               │
               ▼
          [ Wheels ]
```

## Companion App Control Path

The companion Android tablet can also send velocity commands, which flow through the ROS Master:

```
Android App (192.168.8.3)
        │
        │ Wi-Fi (Hotspot) → ROS topics
        ▼
/cmd_frm_tablet  →  ROS Master (Orin NX)
/module_status   →  Navigation toggle
/cmd_vel         →  Direct velocity control
/control         →  General control commands
```

## Key Interfaces Summary

| Layer | Interface | Protocol |
|---|---|---|
| Navigation Stack → Controller | `/cmd_vel` | ROS topic |
| Controller → Hardware Interface | `/wheel_cmd_velocities` | ROS topic |
| Hardware Interface → STM32 | ROSSerial | Serial/USB |
| STM32 → Motor Driver | RS485 | RS485 |
| STM32 → ROS (feedback) | `/measured_joint_states` | ROSSerial → ROS topic |
