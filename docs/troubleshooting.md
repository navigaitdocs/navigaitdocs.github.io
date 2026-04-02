# Troubleshooting

Common problems and their solutions when working with the Navi robot platform.

---

## Connectivity Issues

### Cannot SSH into the robot

**Symptoms:** `ssh: connect to host 192.168.8.1 port 22: Connection refused` or `Network is unreachable`

**Checklist:**

1. Confirm your workstation is connected to the robot's Wi-Fi hotspot — not your home network.
2. Ping the Orin NX first:
   ```bash
   ping 192.168.8.1
   ```
3. If ping fails, the hotspot may not have started. The `hotspot.service` may have failed at boot. Physically access the robot and check:
   ```bash
   systemctl status hotspot.service
   journalctl -u hotspot.service -n 30
   ```
4. Try restarting the hotspot service:
   ```bash
   sudo systemctl restart hotspot.service
   ```

---

### Cannot SSH into the AGX Orin (192.168.8.2)

The AGX Orin is only reachable **from the Orin NX**, not directly from your workstation.

```bash
# Step 1 — SSH into Orin NX first
ssh nvidia@192.168.8.1

# Step 2 — Then hop to AGX Orin
ssh nvidia@192.168.8.2
```

If the hop fails, the Ethernet switch inside the robot may have an issue, or the AGX Orin may have failed to boot. Check the power indicator on the AGX Orin board.

---

## ROS Issues

### `rostopic list` returns nothing or hangs

**Cause:** The ROS Master is not reachable, or the ROS environment is not sourced correctly.

**Fix:**

```bash
# Verify environment
echo $ROS_MASTER_URI
# Expected: http://192.168.8.1:11311

# If empty, source manually
source /opt/ros/noetic/setup.bash

# Test master connectivity
rostopic list
```

If the master is unreachable, check `rehab_boot.service`:

```bash
systemctl status rehab_boot.service
journalctl -u rehab_boot.service -n 50
```

---

### Navigation commands have no effect

**Symptoms:** Publishing to `/module_status` does not start navigation.

**Checklist:**

1. Confirm `rehab_boot.service` is running:
   ```bash
   systemctl status rehab_boot.service
   ```
2. Confirm the topic is active:
   ```bash
   rostopic hz /module_status
   ```
3. Verify the exact message format — quotes matter:
   ```bash
   rostopic pub /module_status std_msgs/String "data: 'NavMode,1'" --once
   ```
4. Check for motor driver issues — if the STM32 is not responding, navigation commands will be accepted but wheels won't move. Check `/measured_joint_states`:
   ```bash
   rostopic echo /measured_joint_states
   ```

---

### ROS topics from the AGX Orin are not visible

**Cause:** `ROS_IP` or `ROS_HOSTNAME` on the AGX Orin is misconfigured, causing topics to publish to the wrong interface.

**Fix — on the AGX Orin:**

```bash
echo $ROS_IP
echo $ROS_HOSTNAME
# Both should be 192.168.8.2
```

If wrong, set them manually and restart the chatbot service:

```bash
export ROS_IP=192.168.8.2
export ROS_HOSTNAME=192.168.8.2
sudo systemctl restart chatbot.service
```

---

## Audio Issues

### No sound from the robot

**Checklist:**

1. Check that the amplifier is powered on:
   ```bash
   rostopic pub /audio_power_control std_msgs/Bool "data: true" --once
   ```
2. Check the current volume is not zero:
   ```bash
   DISPLAY= pactl list sinks | grep 'Volume:' | head -n 1 | awk -F / '{print $2}' | xargs
   ```
3. If volume reads 0%, set it to an audible level:
   ```bash
   DISPLAY= pactl set-sink-volume 0 60%
   ```
4. Check if audio is muted:
   ```bash
   DISPLAY= pactl list sinks | grep 'Mute:'
   # If "Mute: yes", unmute:
   DISPLAY= pactl set-sink-mute 0 0
   ```
5. Check `audio-broadcaster.service` is running:
   ```bash
   systemctl status audio-broadcaster.service
   ```

---

### Microphone input is not being captured

**Checklist:**

1. Confirm `audio-broadcaster.service` is active:
   ```bash
   systemctl status audio-broadcaster.service
   journalctl -u audio-broadcaster.service -n 30
   ```
2. Check if the microphone is muted:
   ```bash
   pactl list sources | grep -A 5 'Name:'
   ```
3. Unmute the default microphone:
   ```bash
   pactl set-source-mute @DEFAULT_SOURCE@ 0
   ```

---

## Camera Issues

### Camera topics show no data

**Orbbec camera:**

```bash
systemctl status orbbec_camera.service
journalctl -u orbbec_camera.service -n 30
```

Check logs at:
- `~/camera_service_output.log`
- `~/camera_service_error.log`

**RealSense camera:**

The RealSense is managed by the `rehab_boot.service` launch file. If this service is healthy but `/camera/color/image_raw` shows no data, check if the USB connection is secure.

```bash
# Check if camera device is detected
lsusb | grep -i intel
lsusb | grep -i orbbec
```

---

## Service Issues

### A service failed to start at boot

```bash
# Check which services failed
systemctl --failed

# Inspect the specific service
systemctl status <service-name>
journalctl -u <service-name> -n 50

# Attempt manual restart
sudo systemctl restart <service-name>
```

---

### `chatbot.service` keeps restarting

**Cause:** A dependency service (Riva, Nano LLM, audio-broadcaster, video-recorder) is not healthy. `chatbot.service` has `Restart=always`, so it loops.

**Fix:**

```bash
# Check all dependencies first
systemctl status riva_container.service
systemctl status nano_llm_tools.service
systemctl status audio-broadcaster.service
systemctl status video-recorder.service

# Fix the failing dependency, then chatbot will stabilize
sudo systemctl restart <failing-service>
```

---

## Battery Issues

### Battery percentage not publishing

```bash
# Check if topic exists
rostopic list | grep battery

# Check cloud_comm service
systemctl status cloud_comm.service
journalctl -u cloud_comm.service -n 30
```

Logs at:
- `~/cloud_comm_output.log`
- `~/cloud_comm_error.log`

---

## Cloud Connectivity Issues

### IoT Hub not receiving telemetry

**Checklist:**

1. Confirm the robot is connected to home Wi-Fi (internet access):
   ```bash
   ping -c 3 8.8.8.8
   ```
2. Check `iothub_monitor.service` on the AGX Orin:
   ```bash
   systemctl status iothub_monitor.service
   journalctl -u iothub_monitor.service -n 30
   ```
3. Check `cloud_comm.service` on the Orin NX:
   ```bash
   systemctl status cloud_comm.service
   ```

---

## General Recovery

If the robot is in an unknown state and you cannot diagnose the issue remotely, perform a **clean reboot**:

```bash
# On Orin NX
sudo reboot

# On AGX Orin (SSH from Orin NX)
ssh nvidia@192.168.8.2
sudo reboot
```

All `systemd` services will restart automatically in the correct order. Allow **2–3 minutes** for full initialization before testing again.

!!! warning "Before Rebooting"
    Ensure all services you may have disabled during testing are **re-enabled** before rebooting:
    ```bash
    sudo systemctl enable chatbot.service
    sudo systemctl enable riva_container.service
    sudo systemctl enable nano_llm_tools.service
    ```
