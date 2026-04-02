# Replacing the Conversational Pipeline

## Overview

If you are building a custom conversational AI to run on the Navi robot, you can disable the default pipeline and replace it with your own. This section describes the steps and integration points required.

!!! warning "Production Reset"
    Always re-enable any disabled services before returning the robot to production use. The boot sequence depends on these services being active.

## Step 1 — Disable Default AI Services

On the AGX Orin, disable the services you are replacing:

```bash
# Disable the conversational AI agent
sudo systemctl disable chatbot.service
sudo systemctl stop chatbot.service

# Optionally disable on-device ASR/TTS if using your own speech engine
sudo systemctl disable riva_container.service
sudo systemctl stop riva_container.service

sudo systemctl disable nano_llm_tools.service
sudo systemctl stop nano_llm_tools.service
```

!!! tip
    If your custom pipeline uses NVIDIA Riva for ASR/TTS, you do **not** need to disable `riva_container.service`. Leave it running and connect to it from your application.

## Step 2 — Microphone Input

Subscribe to the GStreamer audio stream from `audio-broadcaster.service` for microphone input. Keep this service running:

```python
import gi
gi.require_version('Gst', '1.0')
from gi.repository import Gst

Gst.init(None)

pipeline_str = (
    f"udpsrc port={UDP_PORT} ! "
    f"audio/x-raw,format=S16LE,rate=16000,channels=1 ! "
    f"appsink name=sink emit-signals=true"
)

pipeline = Gst.parse_launch(pipeline_str)
pipeline.set_state(Gst.State.PLAYING)
```

## Step 3 — Speaker Output

Use PulseAudio or PyAudio interfaces for speaker output:

### sounddevice (Cloud TTS)

```python
import sounddevice as sd
import numpy as np

stream = sd.OutputStream(
    samplerate=44100,
    channels=2,
    dtype='float32',
    latency='low'
)
stream.start()
```

### PyAudio (Edge TTS)

```python
import pyaudio

p = pyaudio.PyAudio()
stream = p.open(
    format=pyaudio.paInt16,
    channels=1,
    rate=44100,
    output=True
)
```

## Step 4 — Robot Control Integration

Publish to these topics to control the robot from your pipeline:

| Topic | Type | Purpose |
|---|---|---|
| `/chatbot_command` | `std_msgs/String` | Send commands to the navigation/control stack |
| `/feedback_to_tablet` | `std_msgs/String` | Send smiley/status data to the tablet display |
| `/smiley_param` (rosparam) | `string` | Set facial expression on the robot screen |

```python
import rospy
from std_msgs.msg import String

rospy.init_node('my_custom_chatbot')
cmd_pub = rospy.Publisher('/chatbot_command', String, queue_size=10)
feedback_pub = rospy.Publisher('/feedback_to_tablet', String, queue_size=10)

cmd_pub.publish("navigate_to_kitchen")
feedback_pub.publish("smiling")
```

## Step 5 — Receiving App Commands

Subscribe to these topics to receive commands from the companion app:

```python
def cmd_callback(msg):
    print(f"Command from tablet: {msg.data}")

rospy.Subscriber('/cmd_frm_tablet', String, cmd_callback)
rospy.Subscriber('/control', String, cmd_callback)
rospy.Subscriber('/module_status', String, cmd_callback)
```

## Integration Topics Summary

| Topic | Direction | Description |
|---|---|---|
| `/chatbot_command` | Publish | Robot control from your pipeline |
| `/feedback_to_tablet` | Publish | Status/expressions to tablet |
| `/cmd_frm_tablet` | Subscribe | Commands from companion app |
| `/control` | Subscribe | General app control commands |
| `/module_status` | Subscribe | Module toggle commands (e.g., NavMode) |
| `/cmd_vel` | Publish | Direct velocity control |
