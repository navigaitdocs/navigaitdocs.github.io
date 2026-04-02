# Quick Reference

A condensed reference card for common operations on the Navi robot platform.

## SSH Access

| Target | Command |
|---|---|
| ROS Master (Orin NX) | `ssh nvidia@192.168.8.1` |
| AI Board (AGX Orin) | `ssh nvidia@192.168.8.2` (from Orin NX) |
| Default password | `Provided in Email` |

## Head & Neck

| Action | Command |
|---|---|
| Neutral position | `rosparam set /neck_cmd 1` |
| Rotate left | `rosparam set /neck_cmd 3` |
| Rotate right | `rosparam set /neck_cmd 4` |

## Facial Expressions

| Action | Command |
|---|---|
| Set expression | `rosparam set /smiley_param <expression>` |
| Available expressions | `smiling`, `receiving_audio`, `thinking`, `talking`, `waving`, `lowbattery`, `charging` |

## Sensors & Diagnostics

| Action | Command |
|---|---|
| List all ROS topics | `rostopic list` |
| Check battery | `rostopic echo /battery_display` |
| RealSense camera stream | `rostopic echo /camera/color/image_raw` |
| Echo any topic | `rostopic echo /<topic_name>` |
| Check topic frequency | `rostopic hz /<topic_name>` |

## Audio — Volume Control (PulseAudio)

| Action | Command |
|---|---|
| Set volume to 50% | `DISPLAY= pactl set-sink-volume 0 50%` |
| Increase volume by 5% | `DISPLAY= pactl set-sink-volume 0 +5%` |
| Decrease volume by 5% | `DISPLAY= pactl set-sink-volume 0 -5%` |
| Get current volume | `DISPLAY= pactl list sinks \| grep 'Volume:' \| head -n 1 \| awk -F / '{print $2}' \| xargs` |
| Mute | `DISPLAY= pactl set-sink-mute 0 1` |
| Unmute | `DISPLAY= pactl set-sink-mute 0 0` |
| Toggle mute | `DISPLAY= pactl set-sink-mute 0 toggle` |
| Toggle mic mute | `pactl set-source-mute @DEFAULT_SOURCE@ toggle` |

## Service Management

| Action | Command |
|---|---|
| Check service status | `systemctl status <service-name>` |
| View service logs | `journalctl -u <service-name> -f` |
| Restart a service | `sudo systemctl restart <service-name>` |
| Enable a service | `sudo systemctl enable <service-name>` |

## IP Address Map

| Device | IP |
|---|---|
| Jetson Orin NX (ROS Master) | `192.168.8.1` |
| Jetson AGX Orin (AI) | `192.168.8.2` |
| Android Tablet (Robot Screen) | `192.168.8.3` |
