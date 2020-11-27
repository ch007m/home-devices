cat <<EOF > wpa_supplicant.conf
country=BE
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
network={
    ssid="WiFi-2.4-0290_2GEXT"
    psk="w29xnbrcp2j4s"
    key_mgmt=WPA-PSK
}
EOF


- Launch Raspberry Pi Imager 
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

