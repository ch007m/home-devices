## How to install Pi OS

- Launch the application `Raspberry Pi Imager` on your desktop 
  - Select `Raspberry PI No desktop image`
  - Pickup the SD card
  - Write

See : https://www.instructables.com/How-to-Setup-Raspberry-Pi-Without-Monitor-and-Keyb/

- Open a terminal and create the following file under the Volume mounted - boot
  `touch SSH`
- Plug your Pi4 to a network cable and power it up
- ssh to the pi machine
  `ssh pi@raspberrypi.local`
- pwd is `raspberry`

- Add the missing DNS servers to the file `/etc/dhcpcd.conf`
  `# Add DNS Servers
   static domain_name_servers=192.168.1.1 8.8.4.4 8.8.8.8`

- open the `raspi-config` by issuying this command `sudo raspi-config` within the terminal
- Choose `5. interfacing option` and next `P3 VNC`
- Define an IP fix address: https://pimylifeup.com/raspberry-pi-static-ip-address/

### Setup WIFI

```
cat <<EOF > wpa_supplicant.conf
country=BE
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
network={
    ssid="WiFi-2.4-0290_2GEXT"
    psk="w29xnbrcp2j4s"
    key_mgmt=WPA-PSK
}
EOF
```

### LibreElec

- Install the LibreElec image for Kodi `LibreELEC-RPi4.arm-9.2.6.img.gz` using BalenaEtcher or Raspi PI Manager
  on the SD Card
- Insert the card on the Pi4, plug the HDMI cable to a port HDMI supporting your source on the SmartTV 
- Use the TV to [setup LibreElec](https://www.linuxbabe.com/raspberry-pi/libreelec-raspberry-pi) such as network, ...
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
3. Enter the following command to give a list of DMT-supported modes: `/opt/vc/bin/tvservice -m DMT`
4. Enter the following command to show your current state: `/opt/vc/bin/tvservice -s`
5. Enter the following commands to dump more detailed information from your monitor: `/opt/vc/bin/tvservice -d edid.dat; /opt/vc/bin/edidparser edid.dat`
   The edid.dat should also be provided when troubleshooting problems with the default HDMI mode.


https://medium.com/@monofuel34089/running-your-raspberry-pi-4-at-4k60hz-78010a26e98d