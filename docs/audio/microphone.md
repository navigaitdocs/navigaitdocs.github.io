# Microphone Input

## Overview

Audio is captured from the robot's hardware microphone via a **GStreamer broadcaster pipeline**. The `audio-broadcaster.service` runs at boot, captures the microphone using PulseAudio's echo-cancelled source, and fans the stream out to multiple UDP ports simultaneously using a `tee` element. This allows several independent services to consume the same audio without interfering with each other.

## Prerequisites

Before using the microphone in your own code, ensure the following:

!!! warning "Disable the default chatbot first"
    The default `chatbot.service` and `riva_container.service` consume the audio ports. If they are running, your application will conflict with them. Stop them before connecting:

    ```bash
    sudo systemctl stop chatbot.service
    sudo systemctl stop riva_container.service
    ```

!!! important "Keep the broadcaster running"
    Your application depends on `audio-broadcaster.service` being active. Do **not** stop it.

    ```bash
    # Verify the broadcaster is running
    systemctl status audio-broadcaster.service

    # View live logs
    journalctl -u audio-broadcaster.service -f
    ```

## Port Allocation

The broadcaster sends identical copies of the audio stream to the following local UDP ports:

| Port | Service | Description |
|---|---|---|
| `5000` | Raw Recorder | Raw audio for archival and custom processing |
| `5003` | VAD | Voice Activity Detection |

For custom integrations, **connect to port `5000`** unless you specifically need one of the other streams.

## Audio Format

| Parameter | Value |
|---|---|
| **Source** | PulseAudio echo-cancelled source (`source_ec`) |
| **Encoding** | S16LE (16-bit signed little-endian PCM) |
| **Channels** | Mono (1 channel) |
| **Sample Rate** | 16,000 Hz |
| **Transport** | UDP (localhost) |

## How the Broadcaster Works

The broadcaster uses a GStreamer `tee` element to duplicate the microphone signal and send it to all ports simultaneously:

```python
import gi
gi.require_version('Gst', '1.0')
from gi.repository import Gst

Gst.init(None)

pipeline_str = (
    "pulsesrc device=source_ec ! "
    "audio/x-raw,channels=1,rate=16000,format=S16LE ! "
    "audioconvert ! audioresample ! "
    "tee name=t "
    "t. ! queue ! udpsink host=127.0.0.1 port=5000 "  # Raw Recording
    "t. ! queue ! udpsink host=127.0.0.1 port=5001 "  # Cloud ASR
    "t. ! queue ! udpsink host=127.0.0.1 port=5002 "  # Wake Word
)
```

This pipeline is managed by `audio-broadcaster.service` — you do not need to run it yourself.

## Recording from the Stream

To capture audio from the broadcaster in your own application, connect a UDP socket to port `5000` and read the raw PCM bytes:

```python
import socket
import wave

def record_audio(filename="output.wav", duration=5):
    UDP_IP = "127.0.0.1"
    UDP_PORT = 5000
    RATE = 16000  # Must match the broadcaster rate

    sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    sock.bind((UDP_IP, UDP_PORT))

    frames = []
    print(f"Recording from UDP port {UDP_PORT} for {duration} seconds...")

    # Collect packets for the specified duration
    for _ in range(int(RATE / 1024 * duration * 2)):
        data, _ = sock.recvfrom(4096)
        frames.append(data)

    # Write collected frames to a WAV file
    with wave.open(filename, 'wb') as wf:
        wf.setnchannels(1)
        wf.setsampwidth(2)      # 16-bit = 2 bytes
        wf.setframerate(RATE)
        wf.writeframes(b''.join(frames))

    print(f"Saved to {filename}")
```

!!! tip "Muting the microphone"
    To mute the microphone at the OS level without stopping the broadcaster:
    ```bash
    pactl set-source-mute @DEFAULT_SOURCE@ toggle
    ```
