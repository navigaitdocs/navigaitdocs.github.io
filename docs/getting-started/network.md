# Network & Connectivity

## Internal Network Topology

All internal compute devices are connected via an **Ethernet switch** inside the robot chassis. The Jetson Orin NX serves as the ROS Master and provides a Wi-Fi hotspot for development access.

```
                        [ Cloud / Internet ]
                               │
                           Home Wi-Fi
                               │
                      ┌────────▼────────┐
                      │  Jetson Orin NX │◄─── Wi-Fi Hotspot (Dev Access)
                      │  192.168.8.1    │
                      └────────┬────────┘
                               │ Ethernet
                      ┌────────▼────────┐
                      │ Ethernet Switch │
                      └──┬──────────┬──┘
                         │          │
              ┌──────────▼──┐  ┌────▼──────────┐
              │ AGX Orin    │  │ Robot Screen  │
              │ 192.168.8.2 │  │ 192.168.8.3   │
              └─────────────┘  └───────────────┘

                        
```

## External Wi-Fi (Internet Access)

The robot connects to a home or commercial Wi-Fi network for cloud services.

| Feature | Detail |
|---|---|
| Supported bands | 2.4 GHz and 5 GHz |
| Authentication | WPA / WPA2 |
| **Not supported** | WEP, 802.1X enterprise, open networks |

!!! info "Commercial Networks"
    For commercial networks with multiple access points sharing the same SSID, credentials are not an issue. If the SSID changes, new credentials must be provided to the robot.

!!! tip "Offline Operation"
    If external internet is unavailable, **internal inter-device communication continues without interruption**. All ROS traffic flows over the internal Ethernet switch.

## Cloud Communication

The robot communicates with the cloud backend via **Azure IoT Hub** for telemetry, configuration sync, and remote commands.

| Service | Board | Purpose |
|---|---|---|
| `cloud_comm.service` | Orin NX | Battery telemetry to IoT Hub |
| `iothub_monitor.service` | AGX Orin | Bidirectional command/status messaging |

Connection strings are pre-configured on each device — no manual setup is required for cloud connectivity.

## Development Hotspot

The Orin NX broadcasts a Wi-Fi hotspot specifically for development and onboarding access.

!!! warning "Production Note"
    The hotspot connection is intended for **development and onboarding only**. In production, the robot connects to the user's home Wi-Fi for internet access. The internal Ethernet network remains active regardless of external Wi-Fi status.
