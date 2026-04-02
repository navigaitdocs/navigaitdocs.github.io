# Orin NX Services

The Jetson Orin NX is the **ROS Master** board. It runs all core robot control services.

## hotspot.service

| Field | Detail |
|---|---|
| **Purpose** | Initializes the Jetson wireless hotspot for remote SSH and ROS access |
| **Restart Policy** | On boot |
| **Board** | Jetson Orin NX |


```bash
systemctl status hotspot.service
journalctl -u hotspot.service -f
```

## rehab_boot.service

| Field | Detail |
|---|---|
| **Purpose** | Launches primary robotic and autonomous functionalities |
| **Restart Policy** | On boot |
| **Board** | Jetson Orin NX |

```bash
systemctl status rehab_boot.service
journalctl -u rehab_boot.service -f
```

## cloud_comm.service

| Field | Detail |
|---|---|
| **Purpose** | Manages IoT Hub telemetry including battery status payloads |
| **Restart Policy** | On boot |
| **Board** | Jetson Orin NX |

```bash
systemctl status cloud_comm.service
journalctl -u cloud_comm.service -f
```
