Table of Contents
=================

  * [How to install Armbian OS](#how-to-install-armbian-os)
  * [Login the first time](#login-the-first-time)
  * [Device Autodiscovery](#device-autodiscovery)
  * [Import your SSH key](#import-your-ssh-key)
  * [VNC Server](#vnc-server)
  * [Setup Wifi](#setup-wifi)
  * [Mount a disk](#mount-a-disk)
  * [Ip address](#ip-address)
  * [Add missing DNS server](#add-missing-dns-server)

The list of the OS available for Rockpro64 are available [here](https://wiki.pine64.org/index.php/ROCKPro64#Software_and_OS_Image_Builds). For the NAS project, we have decided o investigate 2 of them: 
- Armbian OS
- DietPi

**Important**: As the latest releases of Armbian OS are based on Ubuntu and not Debian anymore, then we cannot use it anymore to install OMV.
We can nevertheless still use the `buster`, `strech` [distributions](https://www.armbian.com/rockpro64/#kernels-archive-all) which still is supported by OMV5 install [script](https://github.com/OpenMediaVault-Plugin-Developers/installScript/blob/master/install)

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
*Remark*: Password needed for the first boot is `1234`. If you reboot, then the OS will ask you to change the `pwd` when it will finalize the installation (new pwd `armbian` for `root`) !!

### Device Autodiscovery

This will allow you to access the board via a hostname instead of an IP address.
- Update APT caches and install `Avahi mDNS server`
```bash
sudo apt update
sudo apt install avahi-daemon
```
**Note**: Avahi config files are hosted under `/etc/avahi`. This is where you can customize the hostname

- Now, from your computer, you may access the board using:
```bash
ssh <username>@rockpro64.local
```
**Note**: Note that the username is the `user` you created.

### Import your SSH key

Import your public key to avoid having to pass the password when you ssh
`ssh-copy-id -i ~/.ssh/id_rsa.pub root@rockpro64.local`

### VNC Server

- Instructions to install a VNC server : https://projetsdiy.fr/armbian-acceder-bureau-distance-orange-pi-tigh-vnc/
  ```bash
  sudo apt-get install tightvncserver
  su -
  adduser vnc
  ...
  Adding user `vnc' ...
  Adding new group `vnc' (1001) ...
  Adding new user `vnc' (1001) with group `vnc' ...
  Creating home directory `/home/vnc' ...
  Copying files from `/etc/skel' ...
  New password:
  Retype new password:
  passwd: password updated successfully
  Changing the user information for vnc
  Enter the new value, or press ENTER for the default
  Full Name []: VNCUser
  Room Number []:
  Work Phone []:
  Home Phone []:
  Other []:
  Is the information correct? [Y/n] Y
  ...
  gpasswd -a vnc sudo
  ```
- To start manually the server
  ```bash
  su - vnc
  vncserver :1
  ```
  **Remark**: Add an alias to your `.bash_aliases` file to start the vnc server using the command `alias start-vnc='runuser -l vnc -c "vncserver :1"'`.
  If you also use `Zsh`, then source also the `.bash_aliases` file like this `source .bash_aliases` within the `.zhrc` file.
  
### Setup Wifi

- As the card does not embed a Wifi adapter, then an external Wifi module is needed and can be plugged using the USB port as described [here]( https://developer.ridgerun.com/wiki/index.php?title=Getting_Started_Guide_for_ROCKPro64)
- Launch Network Manager Text User Interface
  ```bash
  nmtui
  ...
  SSID: WiFi-2.4-0290_2GEXT
  ```
- Search and activate your SSID (wireless network)
- Check you actually have an IP address
  ```bash
  ifconfig
  ```
- You can also check the status of the `Wifi` mode using:
  ```bash
  nmcli radio
  WIFI-HW  WIFI     WWAN-HW  WWAN
  enabled  enabled  enabled  enabled
  ```
- Let's see what's out there... scan for AP's
  ```bash
  nmcli dev wifi list
  IN-USE  BSSID              SSID                 MODE   CHAN  RATE        SIGNAL  BARS  SECURITY
  *       3C:37:86:FA:47:F3  WiFi-2.4-0290_2GEXT  Infra  6     130 Mbit/s  90      ▂▄▆█  WPA2
          F8:08:4F:16:02:96  WiFi-2.4-0290        Infra  6     130 Mbit/s  70      ▂▄▆_  WPA2
          3C:37:86:FA:47:F2  WiFi-5.0-0290_5GEXT  Infra  48    270 Mbit/s  54      ▂▄__  WPA2
          F8:08:4F:16:02:97  WiFi-5.0-0290        Infra  48    540 Mbit/s  19      ▂___  WPA2
  ```
- You can also configure the Wifi device using this command
  ```bash
  nmcli device wifi connect <SSID|BSSID> password <password>  
  ```

**Remark**: Official doc - https://docs.armbian.com/User-Guide_Getting-Started/#how-to-connect-to-wireless

### Mount a disk

See [instructions](../mount-a-disk.md)

### Ip address

See [Instructions](https://docs.armbian.com/User-Guide_Getting-Started/#how-to-set-fixed-ip)

### Add missing DNS server

- Add the missing DNS servers to the file `/etc/dhcpcd.conf` (optional)
  ```bash
  # Add DNS Servers
  static domain_name_servers=192.168.1.1 8.8.4.4 8.8.8.8
  ```
- Restart the service
  ```bash
  sudo service dhcpcd restart
  ```
