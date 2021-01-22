Table of Contents
=================

### How to install DietPi Arm OS

- Download from `dietpi.com`, the OS that we will flash on the `eMMC` card (e.g: [DietPi_ROCKPro64-ARMv8-Buster.img](https://dietpi.com/downloads/images/DietPi_ROCKPro64-ARMv8-Buster.7z))
- Unzip the `xz` compressed  file
- Open `Balena Etcher` and flash the `USB-eMMC` card with the img file

### Automate the setup 

To install on the SBC the needed software:
- NextCloud
- Avahi
- NFS Server
- VNCServer
- Chromium
- Alsa
...
  
we will customize the `dietpi.txt` file flashed within the `/boot` folder.
As we cannot mount the SD card as writable on macos, we will then use vagrant to mount it

Execute then the following instructions as defined with the [Vagrant instruction file](../vagrant/README.md)

### Access the SBC

We are now ready to bootstrap the OS.

- Mount the `USB-eMMC` to the `Rockpro64` SBC like the mouse, RJ45 cable, HDMI cable and boot
- Wait a few minutes till all the software will be installed
- SSH to the VM when work is done and copy your local ssh key
  ```bash
  ssh-copy-id -i ~/.ssh/id_rsa.pub root@192.168.1.200
  ssh root@dietpi.local
  ```
  
### Mount a disk

See [instructions](../mount-a-disk.md)





