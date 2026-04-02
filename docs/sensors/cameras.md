# Camera Systems

## Overview

The robot is equipped with **two camera systems**, both connected via USB to the respective Jetson Device.

## Camera Summary

| Camera | Connection | ROS Topic | Primary Use |
|---|---|---|---|
| **Orbbec (Depth)** | USB → AGX Orin | `/orbbec/camera/color/image_raw/compressed` | Activity recognition, depth sensing |
| **Intel RealSense** | USB → Orin NX | `/camera/color/image_raw/compressed` | Navigation, SLAM, obstacle avoidance |

## Orbbec Depth Camera

The Orbbec camera provides both **color and depth** data. This camera is intended for AI models requiring the image access.

### Accessing Orbbec Data

The Orbbec camera is connected to the **AGX Orin** device. To access camera data on the AGX Orin, use the **rospylib** in your python node. 

```bash
rostopic echo /orbbec/camera/color/image_raw/compressed
```

### Check Stream Frequency

```bash
rostopic hz /orbbec/camera/color/image_raw/compressed
```

## Intel RealSense Camera

The RealSense camera is used for **navigation**.

### Accessing RealSense Data

```bash
rostopic echo /camera/color/image_raw/compressed
```

### Check Stream Frequency

```bash
rostopic hz /camera/color/image_raw/compressed
```

## Important Notes

> **AGX Orin — Accessing Camera Data:**  
> To access camera data on the AGX Orin device, you must use **rospylib**. Always subscribe to the **compressed image topics** (e.g., `/orbbec/camera/color/image_raw/compressed`) instead of the raw topics to achieve the highest possible frame rate.
