Table of Contents
=================

   * [How to install Armbian OS](#how-to-install-pi-os)
   * [Setup WIFI](#setup-wifi)

### How to install Armbian OS

- Download from armbian.com, the OS that we will flash on the eMMC card
- 2 distro exists for the `Focal` distribution: `desktop` or  `server`
- Select one of them and download it
- Unzip the `xz` compressed  file
- Open the application `Balena Etcher` and flash the `USB-eMMC` card
- Unmount the `USB-eMMC` card and plug it to the RockPro64 card
- Attach the mouse, RJ45 cable, HDMI cable and boot

### Login the first time

See doc - https://docs.armbian.com/User-Guide_Getting-Started/#how-to-login

- Install on macos the tool `angryip.org` and scan the IP addresses
- Open a terminal and ssh
```bash
ssh root@192.168.1.46
```
*Remark*: Password needed for the first boot is `1234`. If you reboot, then the OS will ask you to change the pwd

### Device Autodiscovery

This will allow you to access the board via a hostname instead of an IP address.
- Update APT caches and install `Avahi mDNS server`
```bash
sudo apt update
sudo apt install avahi-daemon
```
- Now, from your computer, you may access the board using:
```bash
ssh root@rockpro64.local
```
**Note**: Note that username is the user you created.

### Import your SSH key

Import your public key to avoid having to pass the password when you ssh
`ssh-copy-id -i ~/.ssh/id_rsa.pub root@rockpro64.local`

### VNC Server

- Instructions to install a VNC server : https://projetsdiy.fr/armbian-acceder-bureau-distance-orange-pi-tigh-vnc/
- To start manually the server
```bash
su - vnc
vncserver :1
```
  
### Setup Wifi

- As the card does not embed a Wifi adapter, then an external wifi module is needed and can be plugged using the USB port as described [here]( https://developer.ridgerun.com/wiki/index.php?title=Getting_Started_Guide_for_ROCKPro64)
- Launch Network Manager Text User Interface
```bash
nmtui
```
- Search and activate your SSID (wireless network)
- Check you actually have an IP address
```
ifconfig
```
- You can also check the status of the `Wifi` mode using:
  ```
  nmcli radio
  WIFI-HW  WIFI     WWAN-HW  WWAN
  enabled  enabled  enabled  enabled
  ```
- Let's see what's out there... scan for AP's
  ```
  nmcli dev wifi list
  IN-USE  BSSID              SSID                 MODE   CHAN  RATE        SIGNAL  BARS  SECURITY
  *       3C:37:86:FA:47:F3  WiFi-2.4-0290_2GEXT  Infra  6     130 Mbit/s  90      ▂▄▆█  WPA2
          F8:08:4F:16:02:96  WiFi-2.4-0290        Infra  6     130 Mbit/s  70      ▂▄▆_  WPA2
          3C:37:86:FA:47:F2  WiFi-5.0-0290_5GEXT  Infra  48    270 Mbit/s  54      ▂▄__  WPA2
          F8:08:4F:16:02:97  WiFi-5.0-0290        Infra  48    540 Mbit/s  19      ▂___  WPA2
  ```
- Now, let's connect to WiFi (note, one must be root or sudo access)
```
nmcli device wifi connect <SSID|BSSID> password <password>  
```

### Mount nVME SSD disk

- Check if the SSD disk is well present
```bash
fdisk -l /dev/nvme0n1
Disk /dev/nvme0n1: 931.53 GiB, 1000204886016 bytes, 1953525168 sectors
Disk model: Samsung SSD 970 EVO Plus 1TB
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```



### Ip address

- Instructions: https://docs.armbian.com/User-Guide_Getting-Started/#how-to-set-fixed-ip

### Add missing DNS server

- Add the missing DNS servers to the file `/etc/dhcpcd.conf` (optional)
  ```
  # Add DNS Servers
  static domain_name_servers=192.168.1.1 8.8.4.4 8.8.8.8
  ```
- Restart the service
  ```
  sudo service dhcpcd restart
  ```
