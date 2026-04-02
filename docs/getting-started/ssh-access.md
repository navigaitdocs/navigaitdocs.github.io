# SSH Access

## Prerequisites

Connect your laptop to the robot's Wi-Fi hotspot before attempting SSH.

| Field | Value |
|---|---|
| **SSID** | `XVR_HOTSPOT_bd30d9` |
| **Password** | `Provided in the Email` |

!!! note
    If the robot is connected to your Wi-Fi network, you can SSH into it via its WLAN IP address. To find the robot's WLAN IP, run `ipconfig` on the robot and look for the wireless interface address. In this case, use the WLAN IP for SSH while following the setups below. 

## Connecting to the Master Device (Orin NX)

The primary entry point for all development access is the **Jetson Orin NX** at `192.168.8.1`.

```bash
ssh nvidia@192.168.8.1
```

| Field | Value |
|---|---|
| **Host** | `192.168.8.1` |
| **Username** | `Provided In Email` |
| **Password** | `Provided In Email` |

## Connecting to the AI Compute Board (AGX Orin)

The AGX Orin is not directly exposed to the hotspot. SSH into it **from the Orin NX**:

```bash
# First, SSH into the Orin NX
ssh nvidia@192.168.8.1

# Then, from the Orin NX terminal, SSH into the AGX Orin
ssh nvidia@192.168.8.2
```

!!! note
    You can also connect the AGX Orin directly to your Wi-Fi network using standard Linux CLI commands. Once connected, the device can be accessed over SSH via its WLAN IP, without needing to hop through the Orin NX. Ensure both your laptop and the AGX Orin are on the same Wi-Fi network.

    **Supported network types:** WPA/WPA2 and Open networks. Enterprise security (e.g. 802.1X/PEAP) is not supported.

    **Steps to connect the AGX Orin to Wi-Fi:**

    1. SSH into the AGX Orin via the Orin NX (as shown above), then run the following commands:

    2. List available Wi-Fi networks:
    ```bash
    nmcli device wifi list
    ```

    3. Connect to your Wi-Fi network:
    ```bash
    # For WPA/WPA2 networks
    sudo nmcli device wifi connect "YOUR_SSID" password "YOUR_PASSWORD"

    # For open (password-free) networks
    sudo nmcli device wifi connect "YOUR_SSID"
    ```

    4. Verify the connection and find the assigned WLAN IP:
    ```bash
    ip addr show wlan0
    ```

    Once connected, use the displayed WLAN IP to SSH directly into the AGX Orin from any device on the same network:
    ```bash
    ssh nvidia@<AGX_WLAN_IP>
    ```

## VS Code Remote Development

You can also connect using **VS Code Remote - SSH**:

1. Install the [Remote - SSH](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh) extension
2. Open the Command Palette → `Remote-SSH: Connect to Host...`
3. Enter `nvidia@192.168.8.1`
4. Enter password `As provided in Email` when prompted

## ROS Environment

The `.bashrc` on each board automatically configures the ROS environment for every new terminal session:

| Variable | Value |
|---|---|
| `ROS_MASTER_URI` | `http://192.168.8.1:11311` |

!!! tip
    If you open a new terminal and ROS commands fail, verify the environment is sourced by running `echo $ROS_MASTER_URI`. It should return `http://192.168.8.1:11311`.

