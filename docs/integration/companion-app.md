# Companion App Integration

## Overview

The Navi robot's companion Android application communicates with the ROS Master over the Wi-Fi hotspot using standard ROS topic publish/subscribe patterns. The app runs on a tablet at `192.168.8.3`.

## Communication Architecture

```
Android App (192.168.8.3)
        │
        │ Wi-Fi Hotspot (192.168.8.x)
        ▼
ROS Master (Orin NX — 192.168.8.1)
        │
        │ ROS Topics (pub/sub)
        ▼
Navigation Stack / Control Modules
```

!!! note "Cloud-Independent App Communication"
    All communication between the app and the robot for conversational features goes through the **Azure cloud**, not the local hotspot. The hotspot connection is primarily used for robot control topics and onboarding. The mobile app can connect to any Wi-Fi or 4G network to reach the cloud backend independently.

## Key ROS Topics

### App → Robot (Commands)

| Topic | Type | Description |
|---|---|---|
| `/cmd_frm_tablet` | `std_msgs/String` | General commands from the companion app |
| `/module_status` | `std_msgs/String` | Module toggles (e.g., `NavMode,1` / `NavMode,0`) |
| `/control` | `std_msgs/String` | General robot control commands |
| `/cmd_vel` | `geometry_msgs/Twist` | Manual joystick velocity control |

### Robot → App (Feedback)

| Topic | Type | Description |
|---|---|---|
| `/feedback_to_tablet` | `std_msgs/String` | Status updates and smiley/expression data |
| `/battery_display` | `std_msgs/Float32` | Battery percentage |

## Monitoring App Commands

```bash
# Watch all commands arriving from the tablet
rostopic echo /cmd_frm_tablet

# Watch module status changes
rostopic echo /module_status

# Watch general control commands
rostopic echo /control
```

## Sending Feedback to the App

```bash
# Send an expression/status update to the tablet
rostopic pub /feedback_to_tablet std_msgs/String "data: 'smiling'" --once
```

## Cloud Sync

On every boot, `robot-sync-api.service` fetches the latest configuration from the cloud backend, including:

- Model modes
- Persona settings
- Any parameters updated via the companion app while the robot was offline

This ensures the robot always starts with settings that reflect the most recent app configuration.
