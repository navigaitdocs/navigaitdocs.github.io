# Orin NX Services

The Jetson Orin NX is the **ROS Master** board. It runs all core robot control services.

## hotspot.service

| Field | Detail |
|---|---|
| **Purpose** | Initializes the Jetson wireless hotspot for remote SSH and ROS access |
| **Restart Policy** | On boot |
| **Board** | Jetson Orin NX |

This service calls the `wifi_config_V7.0.py` script to configure and broadcast the Wi-Fi hotspot. It must start before any remote SSH or ROS connections can be made.

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

This is the main ROS launch service. It:

1. Sources the ROS environment
2. Calls the `006_export_ros_ip_aitc.sh` script to fetch and export the Wi-Fi IP
3. Launches `rehab_boot.aitc.launch` — the primary ROS launch file
4. Creates logs at `~/systemd_output.log` and `~/systemd_error.log`

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

This service:

1. Sets up the environment
2. Calls `cloud_comm.sh`, which runs the relevant Python scripts for Azure IoT Hub telemetry
3. Creates logs at `~/cloud_comm_output.log` and `~/cloud_comm_error.log`

```bash
systemctl status cloud_comm.service
journalctl -u cloud_comm.service -f
```

## orbbec_camera.service

| Field | Detail |
|---|---|
| **Purpose** | Launches the Orbbec depth camera driver and ROS node |
| **Restart Policy** | Always (auto-restart on failure) |
| **Board** | Jetson Orin NX |

This service starts the Orbbec camera ROS node, which publishes color and depth image data to:

- `/orbbec/camera/color/image_raw`

```bash
systemctl status orbbec_camera.service
journalctl -u orbbec_camera.service -f
```

## .bashrc Configuration (Orin NX)

Every new terminal on the Orin NX automatically:

1. Sets CUDA environment paths
2. Sources ROS Noetic (`/opt/ros/noetic/setup.bash`)
3. Sets device identification by exporting the device/robot name
4. Enables GUI applications (X11 Display)
5. Sets ROS networking variables: `ROS_MASTER_URI`, `ROS_HOSTNAME`, `ROS_IP`
6. Exports the device MAC address
