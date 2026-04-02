# ROS Topics Reference

## Complete Topic List

The following table lists all primary ROS topics available for integration on the Navi platform.

| Topic | Message Type | Direction | Source | Description |
|---|---|---|---|---|
| `/module_status` | `std_msgs/String` | Publish | Any | Toggle navigation and module modes |
| `/cmd_vel` | `geometry_msgs/Twist` | Pub/Sub | Nav Stack | Velocity commands for the mobile base |
| `/wheel_cmd_velocities` | `custom_msg` | Subscribe | STM32 | Wheel velocity commands from diff_drive controller |
| `/measured_joint_states` | `sensor_msgs/JointState` | Publish | STM32 | Measured wheel encoder joint states |
| `/cliff_sensor` | `custom_msg` | Publish | STM32 | Cliff detection sensor data |
| `/bumper_state` | `custom_msg` | Publish | STM32 | Bumper contact sensor data |
| `/rgb` | `custom_msg` | Subscribe | STM32 | RGB LED color commands |
| `/battery_display` | `std_msgs/Float32` | Publish | Orin NX | Battery percentage (0–100) |
| `/chatbot_command` | `std_msgs/String` | Publish | AGX Orin | Commands from conversational pipeline |
| `/feedback_to_tablet` | `std_msgs/String` | Publish | AGX Orin | Feedback / smiley state to tablet display |
| `/cmd_frm_tablet` | `std_msgs/String` | Subscribe | Tablet | Commands received from the companion app |
| `/control` | `std_msgs/String` | Subscribe | Tablet | General control commands from the mobile app |

## Audio Control Topics

!!! danger "Hardware Amplifier — Do Not Use at Application Level"
    The ROS audio topics control the **hardware amplifier** directly. Incorrect settings can damage speaker hardware. For application-level volume control, use the [PulseAudio commands](../audio/volume-linux.md) instead.

| Topic | Message Type | Description |
|---|---|---|
| `/audio_mute_control` | `std_msgs/Bool` | `true` = Mute, `false` = Unmute |
| `/audio_volume_control` | `std_msgs/Int8` | `0` = Min volume, `100` = Max volume |
| `/audio_power_control` | `std_msgs/Bool` | `true` = Power on, `false` = Power off |

## Neck & Expression Parameters

| Parameter | Type | Description |
|---|---|---|
| `/neck_cmd` | `int` | Head/neck position mode (1–4) |
| `/smiley_param` | `string` | Current facial expression name |
