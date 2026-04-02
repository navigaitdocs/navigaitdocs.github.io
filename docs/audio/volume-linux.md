# Volume Control — Linux (PulseAudio)

## Overview

Volume on the Navi robot is managed at the **OS level via PulseAudio**. This is the recommended method for application-level volume control. All commands use the `DISPLAY=` prefix because the robot runs **headless** (no graphical session).

!!! success "Use This for Application-Level Control"
    For any application that needs to adjust volume, use these PulseAudio commands. Do **not** use the ROS audio topics for application-level volume — those control the hardware amplifier directly and can damage speaker hardware.

## Set Volume (Absolute)

Set the volume of sink `0` (primary audio output) to a specific percentage:

```bash
DISPLAY= pactl set-sink-volume 0 <percentage>%
```

**Examples:**

```bash
# Set volume to 10%
DISPLAY= pactl set-sink-volume 0 10%

# Set volume to 50%
DISPLAY= pactl set-sink-volume 0 50%

# Set volume to 100%
DISPLAY= pactl set-sink-volume 0 100%
```

## Adjust Volume (Relative)

Increase or decrease volume relative to the current level:

```bash
# Increase by 5%
DISPLAY= pactl set-sink-volume 0 +5%

# Decrease by 5%
DISPLAY= pactl set-sink-volume 0 -5%
```

## Retrieve Current Volume

Get the current volume level as a clean percentage string:

```bash
DISPLAY= pactl list sinks | grep 'Volume:' | head -n 1 | awk -F / '{print $2}' | xargs
```

**How it works:**

| Command | Purpose |
|---|---|
| `pactl list sinks` | Dumps all audio output device info |
| `grep 'Volume:'` | Filters to volume lines only |
| `head -n 1` | Takes the first occurrence (default device) |
| `awk -F / '{print $2}'` | Extracts the percentage value |
| `xargs` | Trims surrounding whitespace |

## Mute Audio

```bash
DISPLAY= pactl set-sink-mute 0 1
```

| Value | Effect |
|---|---|
| `1` (or `true`) | Mute on |
| `0` (or `false`) | Mute off |

## Unmute Audio

```bash
DISPLAY= pactl set-sink-mute 0 0
```

!!! note
    Unmuting restores volume to whatever level it was at before muting.

## Toggle Mute State

```bash
DISPLAY= pactl set-sink-mute 0 toggle
```

This switches between muted and unmuted — the most convenient command for a mute/unmute button implementation.

## Mute Default Microphone

```bash
pactl set-source-mute @DEFAULT_SOURCE@ toggle
```

The `@DEFAULT_SOURCE@` shortcut avoids needing to look up the full microphone device name.

## Command Reference Summary

| Action | Command |
|---|---|
| Set volume to 50% | `DISPLAY= pactl set-sink-volume 0 50%` |
| Increase by 5% | `DISPLAY= pactl set-sink-volume 0 +5%` |
| Decrease by 5% | `DISPLAY= pactl set-sink-volume 0 -5%` |
| Get current volume | `DISPLAY= pactl list sinks \| grep 'Volume:' \| head -n 1 \| awk -F / '{print $2}' \| xargs` |
| Mute | `DISPLAY= pactl set-sink-mute 0 1` |
| Unmute | `DISPLAY= pactl set-sink-mute 0 0` |
| Toggle mute | `DISPLAY= pactl set-sink-mute 0 toggle` |
| Toggle mic mute | `pactl set-source-mute @DEFAULT_SOURCE@ toggle` |
