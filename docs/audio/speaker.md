# Speaker Output

## Overview

The Navi robot's speaker system is driven via **Cloud TTS**, streaming raw PCM audio directly to the hardware using the `sounddevice` library. This approach is chosen for two specific reasons:

- **Low latency** — raw PCM skips MP3 decoding entirely
- **Instant interrupt** — the stream can be aborted mid-sentence, unlike buffered players such as MPV

## Prerequisites

Before using the speaker in your own code, ensure the following:

!!! warning "Disable the default chatbot first"
    The `chatbot.service` owns the speaker output at runtime. If it is running, your audio will conflict. Stop it before playing audio from your own code:

    ```bash
    sudo systemctl stop chatbot.service
    ```

!!! important "Keep the broadcaster running"
    The `audio-broadcaster.service` must remain active if your application also uses microphone input alongside speaker output.

    ```bash
    systemctl status audio-broadcaster.service
    ```

## Audio Format

| Parameter | Value |
|---|---|
| **TTS Provider** | ElevenLabs (`eleven_flash_v2_5` model) |
| **Output Format** | Raw PCM (`pcm_44100`) |
| **Sample Rate** | 44,100 Hz |
| **Channels** | Mono (1) |
| **Playback Library** | `sounddevice` |
| **dtype** | `float32` |

## Hardware Path

```
AGX Orin (TTS Audio)
       │
   Audio Out (analog)
       │
  Audio Amplifier Board
       │
     Speakers
```

## Playing TTS Audio

The following function streams text-to-speech from ElevenLabs and plays it directly to the speaker hardware. Audio is converted from 16-bit PCM bytes to `float32` numpy arrays on the fly:

```python
import numpy as np
import sounddevice as sd
from elevenlabs.client import ElevenLabs

def speak_and_play(text, api_key):
    client = ElevenLabs(api_key=api_key)

    # Request raw PCM — eliminates decoding lag
    audio_stream = client.text_to_speech.stream(
        text=text,
        voice_id="TbMNBJ27fH2U0VgpSNko",
        model_id="eleven_flash_v2_5",
        output_format="pcm_44100"
    )

    # Open the speaker hardware and stream chunks directly
    with sd.OutputStream(samplerate=44100, channels=1, dtype='float32') as stream:
        for chunk in audio_stream:
            # Convert 16-bit PCM bytes to float32 in [-1.0, 1.0]
            samples = np.frombuffer(chunk, dtype=np.int16).astype(np.float32) / 32768.0
            stream.write(samples.reshape(-1, 1))
```

Set your ElevenLabs API key via an environment variable rather than hardcoding it:

```bash
export ELEVENLABS_API_KEY="your_api_key_here"
```

```python
import os
api_key = os.getenv("ELEVENLABS_API_KEY")
speak_and_play("Hello, I am Navi.", api_key)
```

## Complete Record & Play Example

The following example ties microphone input and speaker output together: it records 5 seconds of audio from the broadcaster stream on port `5000`, then plays back a confirmation response.

!!! warning "Stop the chatbot before running this"
    ```bash
    sudo systemctl stop chatbot.service
    sudo systemctl stop riva_container.service
    ```

```python
import os
import socket
import wave
import numpy as np
import sounddevice as sd
from elevenlabs.client import ElevenLabs
from dotenv import load_dotenv

load_dotenv()
client = ElevenLabs(api_key=os.getenv("ELEVENLABS_API_KEY"))


def record_from_stream(output_file="test_record.wav", duration=5):
    """Listens to the broadcaster on port 5000 and saves audio to a WAV file."""
    UDP_IP = "127.0.0.1"
    UDP_PORT = 5000
    RATE = 16000  # Must match the broadcaster rate

    sock = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    sock.bind((UDP_IP, UDP_PORT))

    frames = []
    print(f"Recording from UDP port {UDP_PORT} for {duration} seconds...")

    for _ in range(int(RATE / 1024 * duration * 2)):
        data, _ = sock.recvfrom(4096)
        frames.append(data)

    with wave.open(output_file, 'wb') as wf:
        wf.setnchannels(1)
        wf.setsampwidth(2)  # 16-bit
        wf.setframerate(RATE)
        wf.writeframes(b''.join(frames))

    print(f"Saved to {output_file}")


def play_low_latency_tts(text):
    """Streams PCM from ElevenLabs directly to speakers via sounddevice."""
    print(f"Playing: {text}")

    audio_stream = client.text_to_speech.stream(
        text=text,
        voice_id="TbMNBJ27fH2U0VgpSNko",
        model_id="eleven_flash_v2_5",
        output_format="pcm_44100"
    )

    with sd.OutputStream(samplerate=44100, channels=1, dtype='float32') as stream:
        for chunk in audio_stream:
            samples = np.frombuffer(chunk, dtype=np.int16).astype(np.float32) / 32768.0
            stream.write(samples.reshape(-1, 1))


if __name__ == "__main__":
    record_from_stream("user_input.wav", duration=5)
    play_low_latency_tts("I have finished recording your audio from the system stream.")
```
