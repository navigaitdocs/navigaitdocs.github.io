# ROS Environment Setup

## Platform

The Navi robot runs **ROS 1 (Noetic)** with the **Jetson Orin NX** acting as the ROS Master. All ROS topics and parameters are accessible from any device on the internal network that has the ROS environment configured.

## ROS Master URI

```
http://192.168.8.1:11311
```

## Environment Variables

Each terminal session requires the ROS environment to be sourced. The `.bashrc` on each board handles this automatically:

| Variable | Value | Purpose |
|---|---|---|
| `ROS_MASTER_URI` | `http://192.168.8.1:11311` | Points all nodes to the ROS Master |
| `ROS_HOSTNAME` | Local device hostname | Used for ROS networking |
| `ROS_IP` | Local device IP | Used for ROS networking |

## Manual Sourcing

If you need to manually source the ROS environment:

```bash
source /opt/ros/noetic/setup.bash
```

For the custom workspace (on AGX Orin):

```bash
source ~/ros_ws/devel/setup.bash
```

## Verifying the Setup

To confirm your ROS environment is correctly configured:

```bash
# Check ROS master is reachable
rostopic list

# Verify environment variables
echo $ROS_MASTER_URI
echo $ROS_HOSTNAME
echo $ROS_IP
```

!!! note "External Workstations"
    If you want to access the ROS network from a workstation connected to the hotspot, set these environment variables manually before running any `rostopic` or `rosnode` commands:
    ```bash
    export ROS_MASTER_URI=http://192.168.8.1:11311
    export ROS_IP=<your-workstation-ip>
    export ROS_HOSTNAME=<your-workstation-ip>
    ```
