# ROS Environment

## Architecture Overview

Only the **Jetson Orin NX** runs ROS natively. It acts as the **ROS Master** for the entire robot.

The **Jetson AGX Orin** does **not** have ROS installed on the host. Instead, it communicates with the ROS ecosystem on OrinNX via the **rosbridge** server (WebSocket-based), using Python code that calls the `roslibpy` library. This allows the AGX Orin to publish/subscribe to topics and get/set parameters on the OrinNX without a native ROS installation.

| Board | ROS Access | Method |
|---|---|---|
| Jetson Orin NX | Native ROS Noetic | Direct ROS environment |
| Jetson AGX Orin | Via rosbridge | `roslibpy` over WebSocket |

---

## Orin NX — Native ROS Environment

### ROS Master URI

```
http://192.168.8.1:11311
```

### Environment Variables

The `.bashrc` on the Orin NX sources the ROS environment automatically on every terminal session:

| Variable | Value | Purpose |
|---|---|---|
| `ROS_MASTER_URI` | `http://192.168.8.1:11311` | Identifies this device as the ROS Master |
| `ROS_HOSTNAME` | Local device hostname | Used for ROS networking |
| `ROS_IP` | Local device IP | Used for ROS networking |

### Manual Sourcing

If you need to source the ROS environment manually on the Orin NX:

```bash
source /opt/ros/noetic/setup.bash
```

### Verifying the Setup

Run these on the **Orin NX** (or any device with native ROS pointed at the master):

```bash
# Check ROS master is reachable
rostopic list

# Verify environment variables
echo $ROS_MASTER_URI
echo $ROS_HOSTNAME
echo $ROS_IP
```

---

## AGX Orin — ROS Access via rosbridge

The AGX Orin does not run ROS on the host. To interact with ROS topics and parameters, it connects to the **rosbridge WebSocket server** running on the Orin NX.

### rosbridge Endpoint

```
ws://192.168.8.1:9090
```

### Python Example using `roslibpy`

```python
import roslibpy

# Connect to the rosbridge server on OrinNX
client = roslibpy.Ros(host='192.168.8.1', port=9090)
client.run()

# Subscribe to a topic
listener = roslibpy.Topic(client, '/your/topic', 'std_msgs/String')
listener.subscribe(lambda msg: print(f"Received: {msg['data']}"))

# Publish to a topic
publisher = roslibpy.Topic(client, '/your/topic', 'std_msgs/String')
publisher.publish(roslibpy.Message({'data': 'hello from AGX Orin'}))

# Get a ROS parameter
param = roslibpy.Param(client, '/some_param')
print(param.get())

# Set a ROS parameter
param.set('new_value')

client.terminate()
```

!!! note "Install roslibpy on AGX Orin"
    ```bash
    pip install roslibpy
    ```

!!! note "rosbridge must be running on OrinNX"
    The rosbridge WebSocket server is started as part of the normal boot sequence on the Orin NX. If it is not reachable, verify `rehab_boot.service` is running on the Orin NX.

---

## External Workstations (Native ROS)

If you have a workstation with ROS installed and want to connect it to the robot's ROS network via the hotspot:

```bash
export ROS_MASTER_URI=http://192.168.8.1:11311
export ROS_IP=<your-workstation-ip>
export ROS_HOSTNAME=<your-workstation-ip>
```

Then use standard ROS tools (`rostopic`, `rosnode`, etc.) as normal.

!!! tip
    Workstations without ROS can also use the rosbridge WebSocket endpoint (`ws://192.168.8.1:9090`) with `roslibpy`, just like the AGX Orin does.
