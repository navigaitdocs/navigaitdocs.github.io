# SSH Access

## Prerequisites

Connect your workstation to the robot's Wi-Fi hotspot before attempting SSH. The hotspot SSID and password are provided with your robot unit.

## Connecting to the ROS Master (Orin NX)

The primary entry point for all development access is the **Jetson Orin NX** at `192.168.8.1`.

```bash
ssh nvidia@192.168.8.1
```

| Field | Value |
|---|---|
| **Host** | `192.168.8.1` |
| **Username** | `nvidia` |
| **Password** | `nvidia` |

## Connecting to the AI Compute Board (AGX Orin)

The AGX Orin is not directly exposed to the hotspot. SSH into it **from the Orin NX**:

```bash
# First, SSH into the Orin NX
ssh nvidia@192.168.8.1

# Then, from the Orin NX terminal, SSH into the AGX Orin
ssh nvidia@192.168.8.2
```

## VS Code Remote Development

You can also connect using **VS Code Remote - SSH**:

1. Install the [Remote - SSH](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh) extension
2. Open the Command Palette → `Remote-SSH: Connect to Host...`
3. Enter `nvidia@192.168.8.1`
4. Enter password `nvidia` when prompted

## ROS Environment

The `.bashrc` on each board automatically configures the ROS environment for every new terminal session:

| Variable | Value |
|---|---|
| `ROS_MASTER_URI` | `http://192.168.8.1:11311` |
| `ROS_HOSTNAME` | Set to local device hostname |
| `ROS_IP` | Set to local device IP address |

!!! tip
    If you open a new terminal and ROS commands fail, verify the environment is sourced by running `echo $ROS_MASTER_URI`. It should return `http://192.168.8.1:11311`.

## Security Note

!!! warning
    The default credentials (`nvidia` / `nvidia`) are intended for development use only. Change them before deploying in any production or shared environment.
