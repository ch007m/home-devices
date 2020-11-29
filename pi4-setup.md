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
  ```
  # Add DNS Servers
  static domain_name_servers=192.168.1.1 8.8.4.4 8.8.8.8
  ```
- Restart the service
  ```
  sudo service dhcpcd restart
  ```
- Import your public key to avoid to have to pass the password when you ssh
  ```
  ssh-copy-id -i ~/.ssh/id_rsa.pub pi@raspberrypi.local
  /usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/Users/cmoullia/.ssh/id_rsa.pub"
  /usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
  /usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
  pi@raspberrypi.local's password:
  
  Number of key(s) added:        1
  
  Now try logging into the machine, with:   "ssh 'pi@raspberrypi.local'"
  and check to make sure that only the key(s) you wanted were added.
  ```  

### Enable VNC (for desktop installation only)

- open the `raspi-config` by issuing this command `sudo raspi-config` within the terminal
- Choose `5. interfacing option` and next `P3 VNC`
  **Remark**: If you cannot update a package, change the raspbian mirror as described [here](https://raspberrypi.stackexchange.com/questions/103539/could-not-connect-to-raspbian-raspberrypi-org-in-raspberry-pi-3)
  using a mirror: https://www.raspbian.org/RaspbianMirrors/
  ```
  sudo vi /etc/apt/sources.list
  # Using a raspbian mirror: https://www.raspbian.org/RaspbianMirrors/
  deb http://mirror.ox.ac.uk/sites/archive.raspbian.org/archive/raspbian/ buster main contrib non-free rpi
  
  # Official raspberry repo
  # deb http://raspbian.raspberrypi.org/raspbian/ buster main contrib non-free rpi
  ```  

- Define an IP fix address: https://pimylifeup.com/raspberry-pi-static-ip-address/

### Setup WIFI

See [doc](https://raspberrytips.com/raspberry-pi-wifi-setup/)

- To configure it manually, create the following wpa_applicant file
```
cat <<EOF > /etc/wpa_supplicant/wpa_supplicant.conf
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=BE

network={
	ssid="WiFi-2.4-0290_2GEXT"
	psk="xxxxx"
    key_mgmt=WPA-PSK
}
EOF
```
- And reboot the pi box `sudo reboot` and verify that the wifi network is working
  ```
  ifconfig wlan0
  ```
### NFS Server

How to Guide are available:
- https://www.raspberrypi.org/documentation/configuration/nfs.md
- http://doc.ubuntu-fr.org/nfs
- https://pimylifeup.com/raspberry-pi-nfs/

Commands exexuted:
```
sudo apt install nfs-kernel-server
sudo mkdir -p /export/users
sudo chmod -R 777 /export

# Check hard disk mounted and partitions
sudo blkid
/dev/sdb1: LABEL="Elements" UUID="F026D0A626D06F5A" TYPE="ntfs" PTTYPE="atari" PARTLABEL="Elements" PARTUUID="a298b181-7514-4189-8b4c-75b1a8e46835"

sudo fdisk –l
...
Disk /dev/sda: 931.5 GiB, 1000170586112 bytes, 1953458176 sectors
Disk model: My Passport 0830
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 6B429A65-72EA-4239-BD51-EE54AF2D882B

Device      Start        End    Sectors   Size Type
/dev/sda1      40     409639     409600   200M EFI System
/dev/sda2  409640 1953195991 1952786352 931.2G Apple HFS/HFS+

# Create a new folder to mount the external hard disk and mount it
sudo mkdir /mnt/nfsshare
sudo mkdir -p /mnt/nfsshare
sudo chmod -R 777 /mnt/nfsshare
sudo mount /dev/sda2 /mnt/nfsshare

# Bind the volume mounted to the NFS shared
sudo mount --bind /mnt/nfsshare /export/users

# Save the volume mounted to allow to mount it after a reboot
sudo vi /etc/fstab
/home/users    /export/users   none    bind  0  0

# Disable SVCGSSD daemon
sudo vi /etc/default/nfs-kernel-server
NEED_SVCGSSD="No"

# To export our directories to a local network 192.168.1.0/24, we add the following two lines to /etc/exports

/export       192.168.1.0/24(rw,fsid=0,insecure,no_subtree_check,async)
/export/users 192.168.1.0/24(rw,nohide,insecure,no_subtree_check,async)
```

### Automount volume

How to guide:

- https://pimylifeup.com/raspberry-pi-mount-usb-drive/
- https://thepihut.com/blogs/raspberry-pi-tutorials/how-to-mount-an-external-hard-drive-on-the-raspberry-pi-raspian

- If you want to check where your drive has been mounted, you can simply use the following command.
  
  sudo fdisk -l
  Disk /dev/sda: 1.8 TiB, 2000365289472 bytes, 3906963456 sectors
  Disk model: Elements 2620
  Units: sectors of 1 * 512 = 512 bytes
  Sector size (logical/physical): 512 bytes / 512 bytes
  I/O size (minimum/optimal): 512 bytes / 512 bytes
  Disklabel type: gpt
  Disk identifier: D9C739C2-7CF1-4C5E-BDBC-7644928A2D8F
  
  Device      Start        End    Sectors  Size Type
  /dev/sda1      40     409639     409600  200M EFI System
  /dev/sda2  409640 3906961407 3906551768  1.8T Microsoft basic data
  


  

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