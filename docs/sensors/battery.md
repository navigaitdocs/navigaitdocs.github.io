# Battery Monitoring

## Overview

Battery status is available through two channels: a **ROS topic** for local access and **Azure IoT Hub** for remote cloud monitoring.

## ROS Topic

| Field | Value |
|---|---|
| **Topic** | `/battery_display` |
| **Message Type** | `std_msgs/Float32` |
| **Direction** | Publish |
| **Value Range** | `0.0` – `100.0` (percentage) |

### Check Battery Percentage

```bash
rostopic echo /battery_display
```

### Monitor Continuously

```bash
# Watch battery level update in real time
rostopic echo /battery_display
```

### Check Publish Frequency

```bash
rostopic hz /battery_display
```

## Cloud Telemetry

Battery status is also reported to **Azure IoT Hub** via `cloud_comm.service` running on the Orin NX. This enables remote battery monitoring from the companion mobile app or backend dashboard, independent of a local ROS connection.

## Low Battery Expression

When battery is low, the facial display expression can be set to `lowbattery`:

```bash
rosparam set /smiley_param lowbattery
```

The conversational AI pipeline handles this automatically when battery telemetry crosses a threshold.
