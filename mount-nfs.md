Table of Contents
=================

   * [Mount a disk and enable NFS service](#mount-a-disk-and-enable-nfs-service)

## Mount a disk and enable NFS service

How to Guides to configure NFS or mount a disk are available:
- https://www.raspberrypi.org/documentation/configuration/nfs.md
- https://pimylifeup.com/raspberry-pi-nfs/
- https://pimylifeup.com/raspberry-pi-mount-usb-drive/
- https://thepihut.com/blogs/raspberry-pi-tutorials/how-to-mount-an-external-hard-drive-on-the-raspberry-pi-raspian

Install first the NFS server
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
- To mount the USB Drive to the Raspberry Pi, create a directory & change the owner permissions: 
  ```
  sudo mkdir -p /mnt/dabou-media
  sudo chown -R pi:pi /mnt/
  sudo chown -R pi:pi /mnt/dabou-media
  ```
- Retrieve now the `UUID` and the `type` of the disk `/dev/sda*` mounted in order to configure `fstab`
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