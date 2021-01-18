Table of Contents
=================

   * [How to install Armbian OS](#how-to-install-pi-os)
   * [Setup WIFI](#setup-wifi)

### How to install Armbian OS

- Download from armbian.com, the OS that we will flash on the eMMC card
- 2 distro exists for the `Focal` distribution: `desktop` or  `server`
- Select one of them and dowload it
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
  
### Setup wifi (need a module)

- Connect to WiFi - https://developer.ridgerun.com/wiki/index.php?title=Getting_Started_Guide_for_ROCKPro64
- Wifi: https://forum.armbian.com/topic/13628-help-me-to-setup-a-wifi-ap-via-command-line/

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
