# Operational Guidelines

## Critical Rules

!!! danger "Do Not Modify Systemd Services"
    Never modify the existing systemd service configurations. Changes to service unit files can break the boot sequence and leave the robot in an unrecoverable state without a manual reboot and repair.

!!! danger "Always Re-enable Services Before Reboot"
    If you disable a service for testing, you **must** re-enable it before rebooting. Leaving core services disabled will result in incomplete system initialization. A robot that boots without its core services may not be reachable or functional.

!!! warning "Do Not Conflict with the Internal Subnet"
    The robot's internal IP space (`192.168.8.x`) is reserved. Do not configure conflicting subnets on the home or lab network.


## Development Best Practices

| Practice | Guidance |
|---|---|
| **Debugging** | Use `rostopic list` and `rostopic echo` for real-time data inspection |
| **Battery** | Monitor `/battery_display` regularly during extended testing sessions |
| **SSH sessions** | Use tmux or screen to keep sessions alive during long operations |
| **Service logs** | Always check `journalctl -u <service> -f` when a service misbehaves |
| **Network** | Verify `ROS_MASTER_URI`, `ROS_IP`, and `ROS_HOSTNAME` are set correctly in each terminal |

## Recommended Debugging Workflow

1. **Check if ROS is running:**
   ```bash
   rostopic list
   ```

2. **Check service health:**
   ```bash
   systemctl status rehab_boot.service
   systemctl status chatbot.service
   ```

3. **Check recent logs:**
   ```bash
   journalctl -u rehab_boot.service --since "5 minutes ago"
   ```

4. **Verify network:**
   ```bash
   ping 192.168.8.1
   ping 192.168.8.2
   ```

5. **Check battery:**
   ```bash
   rostopic echo /battery_display
   ```
