# Camera Systems

## Overview

The robot is equipped with **two camera systems**, both connected via USB to the Jetson Orin NX.

## Camera Summary

| Camera | Connection | ROS Topic | Primary Use |
|---|---|---|---|
| **Orbbec (Depth)** | USB → Orin NX | `/orbbec/camera/color/image_raw` | Activity recognition, depth sensing |
| **Intel RealSense** | USB → Orin NX | `/camera/color/image_raw` | Navigation, SLAM, obstacle avoidance |

## Orbbec Depth Camera

The Orbbec camera provides both **color and depth** data. It is primarily used by the AI pipeline for:

- Activity of Daily Living (ADL) recognition
- Person detection (YOLOv8-based)
- Depth-based spatial awareness

### Accessing Orbbec Data

```bash
rostopic echo /orbbec/camera/color/image_raw
```

### Check Stream Frequency

```bash
rostopic hz /orbbec/camera/color/image_raw
```

## Intel RealSense Camera

The RealSense camera is used for **navigation and mapping**. It is integrated with the SlamKit module for SLAM-based navigation.

### Accessing RealSense Data

```bash
rostopic echo /camera/color/image_raw
```

### Check Stream Frequency

```bash
rostopic hz /camera/color/image_raw
```

!!! note "SlamKit Integration"
    The RealSense camera is used by the SlamKit module for SLAM-based navigation and mapping. Do not stop or interfere with the RealSense stream while autonomous navigation is active.

## Camera Services

The Orbbec camera is managed by `orbbec_camera.service` on the Orin NX, which starts automatically at boot:

```bash
# Check camera service status
systemctl status orbbec_camera.service
```
