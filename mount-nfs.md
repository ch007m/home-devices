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
  ```bash
  sudo apt install nfs-kernel-server
  ```
**Trick**: To be able to use the NTFS format on your Raspberry Pi, you will need to install the NTFS-3g driver.
  ```bash
  apt install ntfs-3g
  ```
**Trick**: To add support for the `exFAT` filesystem, we will need to install two packages.
  ```bash
  apt install exfat-fuse -y
  apt install exfat-utils -y
  ```  
**Trick**: To support read-write access for Mac `hfsplus` format, install the following package and if needed force to `remount` the volume
  ```bash
  apt-get install hfsprogs
  mount -t hfsplus -o remount,force,rw /media/dabou2
  ```

- Format the USB Hard drive as `ext4` using this command `sudo mkfs.ext4 /dev/sda`
- Now that we have installed the NFS server-side software, we can proceed to set up an NFS share on the Raspberry Pi.
- To mount the USB Drive to the Raspberry Pi, create a directory & change the owner permissions: 
  ```bash
  sudo mkdir -p /media/dabou
  sudo chown -R <user>:<group> /media/dabou
  ```
- Change the permissions of the directory
  ```bash
  sudo find /media/dabou/ -type d -exec chmod 755 {} \;
  sudo find /media/dabou/ -type f -exec chmod 644 {} \;
  ```
- Retrieve now the `UUID` and the `type` of the disk `/dev/sda*` in order to configure `fstab` qnd mount the disk
  ```bash
  sudo blkid
  /dev/sda2: LABEL="Elements" UUID="18038afa-7403-44d4-abb5-f1d530e6060" TYPE="ext4"
  ```
**Remark**: Please make a note of the value for both the `UUID` and the `TYPE`

- Modify the `fstab` file to [mount automatically](https://www.shellhacks.com/raspberry-pi-mount-usb-drive-automatically/) the USB drive by running the command below
  ```bash
  vi /etc/fstab
  # External HD USB disk
  UUID=18038afa-7403-44d4-abb5-f1d530e6060 /media/dabou ext4 defaults,auto,users,rw,nofail 0 0
  ```
  or
  ```bash
  echo "UUID=18038afa-7403-44d4-abb5-f1d530e6060 /media/dabou ext4 defaults,auto,users,rw,nofail 0 0" >> /etc/fstab
  ```
- Mount it
  ```bash
  mount -a
  ```
- Edit the `/etc/exports` to mount `/media/dabou`
  ```bash
  echo "/media/dabou *(rw,all_squash,insecure,async,no_subtree_check,anonuid=1000,anongid=1000)" >> /etc/exports
  ```
- Reconfigure the files using `sudo exportfs -ra`
- Restart the service
  ```bash
  systemctl restart nfs-kernel-server
  systemctl status nfs-kernel-server
  ```
- If the speed transfer is very slow, then execute the following instructions: https://www.raspberrypi.org/forums/viewtopic.php?f=28&t=245931&sid=2d40fbd4bef6868d3911c98705e53f0e

- All in one steps
```bash
UUID=$(blkid | sed -n '/nvme0n1/s/.*UUID=\"\([^\"]*\)\".*/\1/p')
echo "UUID="${UUID}" /media/dabou ext4 defaults 0 0" >> /etc/fstab
echo "/media/dabou *(rw,all_squash,insecure,async,no_subtree_check,anonuid=1000,anongid=1000)" >> /etc/exports
mkdir -p /media/dabou
find /media/dabou/ -type d -exec chmod 755 {} \;
find /media/dabou/ -type f -exec chmod 644 {} \;
mount -a
systemctl restart nfs-kernel-server
```
## Fine tuning

- [Benchmark](https://serverfault.com/questions/324438/measure-benchmark-the-speed-latency-of-file-access-on-a-mounted-nfs-share)
- https://www.slashroot.in/how-do-linux-nfs-performance-tuning-and-optimization
- https://photographylife.com/afp-vs-nfs-vs-smb-performance