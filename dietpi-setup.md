Table of Contents
=================

### How to install DietPi Arm OS

- Download from `dietpi.com`, the OS that we will flash on the eMMC card (e.g: DietPi_ROCKPro64-ARMv8-Buster.img)
- Unzip the `xz` compressed  file
- Open the application `Balena Etcher` and flash the `USB-eMMC` card

### Automate the setup 

Deploy the customized `dietpi.txt` file created under this project to the flashed `/boot` folder
using a LinuxOS where the USB card has been mounted

```bash
ssh pi@raspberrypi.local "sudo mkdir /mnt/usb"
ssh pi@raspberrypi.local "sudo mount /dev/sda1 /mnt/usb"
ssh pi@raspberrypi.local "sudo chmod 666 /mnt/usb/boot/dietpi.txt"
sshpass -p "raspberry" scp config/dietpi.txt pi@raspberrypi.local:/mnt/usb/boot
```

### Boot the OS

Unmount the `USB-eMMC` cardfrom the `RaspberryPi` card and plug it to the `Rockpro64` SBC
Attach the mouse, RJ45 cable, HDMI cable and boot

