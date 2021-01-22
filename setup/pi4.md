Table of Contents
=================

   * [How to install Pi OS](#how-to-install-pi-os)
   * [Setup WIFI](#setup-wifi)

### How to install Pi OS

- Launch the application `Raspberry Pi Imager` on your desktop 
  - Select the downloaded file of the `Raspberry PI Buster image` (e.g. 2020-08-20-raspios-buster-armhf.zip) usign the option `Custom`
  - As an alternative, you can select it from the list of the image available but then an internet access is needed
  - Pickup the SD card
  - Write

See : https://www.instructables.com/How-to-Setup-Raspberry-Pi-Without-Monitor-and-Keyb/

- Open a terminal and create the following file under the Volume mounted - boot
  `touch /Volumes/boot/SSH`
- Plug your Pi4 to a network and power it up
- ssh to the pi machine
  `ssh pi@raspberrypi.local`
- pwd is `raspberry`

- Import your public key to avoid having to pass the password when you ssh
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
- Next start the `wlan0` interface and reboot the pi box
  ```
  sudo rfkill unblock wifi; sudo rfkill unblock all
  sudo ifconfig wlan0 down
  sudo ifconfig wlan0 up
  sudo reboot
  ```