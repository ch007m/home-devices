## Instructions to create a USB Bootable card

- Download the Debian daily files: `partition.img.gz` and `firmware.rockpro64-rk3399.img.gz` to create for Arm64 architecture a USB SD Card bootable
```bash
URL=https://d-i.debian.org/daily-images/arm64/daily/netboot/SD-card-images
wget ${URL}/partition.img.gz
wget ${URL}/firmware.rockpro64-rk3399.img.gz
gzcat firmware.rockpro64-rk3399.img.gz partition.img.gz > rockpro64_debian.img
#sudo dd if=rockpro64_debian.img of=/dev/disk2 bs=4M
```
- Open `Balena Etcher` and flash the `USB-SD` card with the img file created

**WARNING**: The image created is not enough as some additional files should be added :-( - see: https://forum.pine64.org/showthread.php?tid=9744