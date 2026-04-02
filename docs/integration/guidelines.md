# Operational Guidelines

## Critical Rules

!!! danger "Do Not Modify Systemd Services"
    Never modify the existing systemd service configurations. Changes to service unit files can break the boot sequence and leave the robot in an unrecoverable state without a manual reboot and repair.

!!! danger "Always Re-enable Services Before Reboot"
    If you disable a service for testing, you **must** re-enable it before rebooting. Leaving core services disabled will result in incomplete system initialization. A robot that boots without its core services may not be reachable or functional.

!!! warning "Do Not Conflict with the Internal Subnet"
    The robot's internal IP space (`192.168.8.x`) is reserved. Do not configure conflicting subnets on the home or lab network.

## Service Safety Checklist

Before any system restart, verify all services are enabled:

```bash
# On Orin NX
systemctl is-enabled hotspot.service
systemctl is-enabled rehab_boot.service
systemctl is-enabled cloud_comm.service
systemctl is-enabled orbbec_camera.service

# On AGX Orin
systemctl is-enabled chatbot.service
systemctl is-enabled audio-broadcaster.service
systemctl is-enabled riva_container.service
systemctl is-enabled nano_llm_tools.service
systemctl is-enabled iothub_monitor.service
systemctl is-enabled video-recorder.service
```

All services should return `enabled` (or `static` for oneshot services).

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

## Integration Partner Scope

This hardware interface guide covers everything needed to build custom applications on the Navi platform. The following are **not accessible** to integration partners:

- The proprietary Navi conversational AI application layer
- Internal LangGraph pipeline implementation
- Neo4j memory system internals
- Azure IoT Hub connection strings (pre-configured on device)
