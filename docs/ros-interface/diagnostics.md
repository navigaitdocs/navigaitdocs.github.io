# Diagnostic Utilities

## Overview

Standard ROS command-line tools can be used to monitor, inspect, and debug the robot's live data streams in real time.

## Common Commands

### List All Active Topics

```bash
rostopic list
```

### Echo Data from a Topic

```bash
rostopic echo /<topic_name>
```

### Check Topic Publish Frequency

```bash
rostopic hz /<topic_name>
```

### Inspect Topic Message Type

```bash
rostopic type /<topic_name>
```

### Get Topic Info

```bash
rostopic info /<topic_name>
```

## Useful Monitoring Commands

### Battery

```bash
rostopic echo /battery_display
```

Returns the current battery percentage as a `Float32` value.

### Orbbec Camera

```bash
rostopic echo /orbbec/camera/color/image_raw
```

Streams raw color image data from the Orbbec depth camera.

### RealSense Camera

```bash
rostopic echo /camera/color/image_raw
```

Streams raw color image data from the Intel RealSense camera.

### Cliff Sensors

```bash
rostopic echo /cliff_sensor
```

Streams real-time cliff detection data from the STM32.

### Bumpers

```bash
rostopic echo /bumper_state
```

Streams bumper contact event data.

### Wheel State

```bash
rostopic echo /measured_joint_states
```

Streams wheel encoder positions and velocities.

## Node Inspection

```bash
# List all active ROS nodes
rosnode list

# Get info on a specific node
rosnode info /<node_name>

# Ping a node to check it is alive
rosnode ping /<node_name>
```

## ROS Parameter Server

```bash
# List all parameters
rosparam list

# Get a specific parameter value
rosparam get /neck_cmd
rosparam get /smiley_param

# Dump all parameters to a YAML file
rosparam dump params.yaml
```
