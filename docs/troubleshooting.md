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

## Audio Issues

### No sound from the robot

**Checklist:**

1. Check the current volume is not zero:
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
