Table of Contents
=================

   * [How to install Pi OS](#how-to-install-pi-os)
   * [Setup WIFI](#setup-wifi)
   * [Mount a disk and enable NFS service](#mount-a-disk-and-enable-nfs-service)
   * [LibreElec](#libreelec)
   * [Check TV setup](#check-tv-setup)
   * [Which values are valid for my monitor?](#which-values-are-valid-for-my-monitor)

### How to install Pi OS

- Launch the application `Raspberry Pi Imager` on your desktop 
  - Select the downloaded file of the `Raspberry PI Buster image` (e.g. 2020-08-20-raspios-buster-armhf.zip) usign the option `Custom`
  - As an alternative, you can select it from the list of the image available but then an internet access is needed
  - Pickup the SD card
  - Write

See : https://www.instructables.com/How-to-Setup-Raspberry-Pi-Without-Monitor-and-Keyb/

- Open a terminal and create the following file under the Volume mounted - boot
  `touch /Volumes/boot/SSH`
- Plug your Pi4 to a network cable and power it up
- ssh to the pi machine
  `ssh pi@raspberrypi.local`
- pwd is `raspberry`

- Import your public key to avoid to have to pass the password when you ssh
  `ssh-copy-id -i ~/.ssh/id_rsa.pub pi@raspberrypi.local` 

- Update first your OS installed if not yet already done
  ```
  sudo apt-get update
  sudo apt-get upgrade
  ```

  **Remark**: If you cannot update a package due to such an error `  Cannot initiate the connection to raspbian.raspberrypi.org:80 (2a00:1098:0:80:1000:75:0:3). - connect (101: Network is unreachable) Could not connect to raspbian.raspberrypi.org:80 (93.93.128.193), connection timed out` , change the raspbian mirror as described [here](https://raspberrypi.stackexchange.com/questions/103539/could-not-connect-to-raspbian-raspberrypi-org-in-raspberry-pi-3)
  using a mirror: https://www.raspbian.org/RaspbianMirrors/
  ```
  sudo vi /etc/apt/sources.list
  # Using a raspbian mirror: https://www.raspbian.org/RaspbianMirrors/
  deb http://mirror.ox.ac.uk/sites/archive.raspbian.org/archive/raspbian/ buster main contrib non-free rpi
  
  # Official raspberry repo
  # deb http://raspbian.raspberrypi.org/raspbian/ buster main contrib non-free rpi
  ``` 
- Add the missing DNS servers to the file `/etc/dhcpcd.conf` (optional)
  ```
  # Add DNS Servers
  static domain_name_servers=192.168.1.1 8.8.4.4 8.8.8.8
  ```
- Restart the service
  ```
  sudo service dhcpcd restart
  ```   
  
- To define an IP fix address, follow the instructions defined [here](https://pimylifeup.com/raspberry-pi-static-ip-address/)

### Setup WIFI

See [How To guide for more information](https://raspberrytips.com/raspberry-pi-wifi-setup/)

- To configure it manually, create the following wpa_applicant file and change the country ISO code, SSID and psk
```
sudo bash -c 'cat <<EOF > /etc/wpa_supplicant/wpa_supplicant.conf
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=BE

network={
	ssid="yyyyyyyy"
	psk="xxxxxxx"
    key_mgmt=WPA-PSK
}
EOF'
```
- Next start the `wlan0` interface and reboot the pi box `sudo reboot`
  ```
  sudo rfkill unblock wifi; sudo rfkill unblock all
  sudo ifconfig wlan0 down
  sudo ifconfig wlan0 up
  sudo reboot
  ```
### Mount a disk and enable NFS service

How to Guides to configure NFS or mount a disk are available:
- https://www.raspberrypi.org/documentation/configuration/nfs.md
- https://pimylifeup.com/raspberry-pi-nfs/
- https://pimylifeup.com/raspberry-pi-mount-usb-drive/
- https://thepihut.com/blogs/raspberry-pi-tutorials/how-to-mount-an-external-hard-drive-on-the-raspberry-pi-raspian

- Install first the NFS server
  ```
  sudo apt install nfs-kernel-server
  ```

**Trick**: To be able to use the NTFS format on your Raspberry Pi, you will need to install the NTFS-3g driver.
  ```
  sudo apt install ntfs-3g
  ```
**Trick**: To add support for the `exFAT` filesystem, we will need to install two packages.
  ```
  sudo apt install exfat-fuse -y
  sudo apt install exfat-utils -y
  ```  

- Format the USB Hard drive as `ext4` using this command `sudo mkfs.ext4 /dev/sda`
- Now that we have installed the NFS server-side software, we can proceed to set up an NFS share on the Raspberry Pi.
- To Mount the USB Drive to the Raspberry Pi, create a directory, change the owner permissions: 
  ```
  sudo mkdir -p /mnt/dabou-media
  sudo chown -R pi:pi /mnt/
  sudo chown -R pi:pi /mnt/dabou-media
  ```
- Retrieve now the UUID of the disk `/dev/sda*` mounted in order to configure `fstab`
  ```
  sudo blkid /dev/sda*
  /dev/sda2: LABEL="Elements" UUID="18038afa-7403-44d4-abb5-f1d530e6060" TYPE="ext4"
  ```
**Remark**: Please make a note of the value for both the `UUID` and the `TYPE`

- Modify the `fstab` file to [mount automatically](https://www.shellhacks.com/raspberry-pi-mount-usb-drive-automatically/) the USB drive by running the command below
  ```
  sudo vi /etc/fstab
  # External HD USB disk
  UUID=18038afa-7403-44d4-abb5-f1d530e6060 /mnt/dabou-media ext4 defaults,auto,users,rw,nofail 0 0
  ```
- Change the permissions of the directory mounted
  ```
  sudo find /mnt/dabou-media/ -type d -exec chmod 755 {} \;
  sudo find /mnt/dabou-media/ -type f -exec chmod 644 {} \;
  ```
- Edit the `/etc/exports` to mount `/mnt/dabout-media`
  ```
  sudo vi /etc/exports
  /mnt/dabou-media *(rw,all_squash,insecure,async,no_subtree_check,anonuid=1000,anongid=1000)
  ```
- Reconfigure the files using `sudo exportfs -ra`
- Restart the service
  ```
  sudo systemctl restart nfs-kernel-server
  sudo systemctl status nfs-kernel-server
  ```
- If the speed transfer is very slow, then execute the following instructions: https://www.raspberrypi.org/forums/viewtopic.php?f=28&t=245931&sid=2d40fbd4bef6868d3911c98705e53f0e

### LibreElec

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


https://medium.com/@monofuel34089/running-your-raspberry-pi-4-at-4k60hz-78010a26e98d