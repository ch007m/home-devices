Table of Contents
=================

  * [LibreElec](#libreelec)
     * [Check TV setup](#check-tv-setup)
     * [Which values are valid for my monitor?](#which-values-are-valid-for-my-monitor)

## LibreElec

- Install the LibreElec image for Kodi `LibreELEC-RPi4.arm-9.2.6.img.gz` using BalenaEtcher or Raspi PI Manager
  on the SD Card
- Insert the card on the Pi4, plug the HDMI cable to a port HDMI supporting your source on the SmartTV 
- Use the remote control of the TV to [setup LibreElec](https://www.linuxbabe.com/raspberry-pi/libreelec-raspberry-pi) such as network, ...
- SSH to the LibreElec VM
  ```
  ssh root@192.168.1.20
  pwd: librelec
  ```
- Edit the config.txt file to add the missing [parameter](https://blog.codetitans.pl/post/howto-enable-4k60hz-on-raspberry-pi-4/)
  More info is available [here](https://www.raspberrypi.org/documentation/configuration/config-txt/video.md) within the Pi official doc
```
mount -o remount,rw /flash
vi /flash/config.txt

# Append to the end og the file
hdmi_enable_4kp60=1

# The hdmi_group command defines the HDMI output group to be either CEA (Consumer Electronics Association
# the standard typically used by TVs) or DMT (Display Monitor Timings, the standard typically used by monitors).
# This setting should be used in conjunction with hdmi_mode.
hdmi_group=1

# hdmi_mode defines the HDMI output format. Format mode numbers are derived from the CTA specification found [here]()
```

### Check TV setup

```
tvservice -l
1 attached device(s), display ID's are :
Display Number 2, type HDMI 0

tvservice -s
state 0xa [HDMI CUSTOM RGB lim 16:9], 1920x1080 @ 60.00Hz, progressive
```

Such setup corresponds to the `hdmi_group=2` and `hdmi_mode=82`

### Which values are valid for my monitor?

Your HDMI monitor may only support a limited set of formats. To find out which formats are supported, use the following method:

1. Set the output format to VGA 60Hz (hdmi_group=1 and hdmi_mode=1) and boot up your Raspberry Pi
2. Enter the following command to give a list of CEA-supported modes: `/opt/vc/bin/tvservice -m CEA`
   ```
   tvservice -m CEA
   Group CEA has 21 modes:
              mode 1: 640x480 @ 60Hz 4:3, clock:25MHz progressive
              mode 2: 720x480 @ 60Hz 4:3, clock:27MHz progressive
              mode 3: 720x480 @ 60Hz 16:9, clock:27MHz progressive
              mode 4: 1280x720 @ 60Hz 16:9, clock:74MHz progressive
              mode 5: 1920x1080 @ 60Hz 16:9, clock:74MHz interlaced
              mode 6: 720x480 @ 60Hz 4:3, clock:27MHz x2 interlaced
              mode 7: 720x480 @ 60Hz 16:9, clock:27MHz x2 interlaced
     (prefer) mode 16: 1920x1080 @ 60Hz 16:9, clock:148MHz progressive
   ```
3. Enter the following command to give a list of DMT-supported modes: `/opt/vc/bin/tvservice -m DMT`
4. Enter the following command to show your current state: `/opt/vc/bin/tvservice -s`
5. Enter the following commands to dump more detailed information from your monitor: `/opt/vc/bin/tvservice -d edid.dat; /opt/vc/bin/edidparser edid.dat`
   The edid.dat should also be provided when troubleshooting problems with the default HDMI mode.

     