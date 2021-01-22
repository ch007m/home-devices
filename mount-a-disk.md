## Mount a disk

- Check if the disks (ssd, external) are well present
```bash
fdisk -l /dev/nvme0n1
Disk /dev/nvme0n1: 931.53 GiB, 1000204886016 bytes, 1953525168 sectors
Disk model: Samsung SSD 970 EVO Plus 1TB
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
...
Disk /dev/sda: 1.84 TiB, 2000365289472 bytes, 3906963456 sectors
Disk model: Elements 2620
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: C63544E0-2A68-4733-87D3-AEE21A5494CB
```
- Get the UUID for the drive that you want to mount
```bash
blkid
...
/dev/sda2: UUID="44788b34-78e3-3cf9-a98d-07cbb18f8fee" LABEL="Dabou2" TYPE="hfsplus" PARTUUID="eac98ec4-4f16-4879-ba61-aa472304c5a3"
```
- Edit the file `fstab` and add a new line in this file containing the following information: - [see doc](https://techguides.yt/guides/how-to-partition-format-and-auto-mount-disk-on-ubuntu-20-04/)
```bash
UUID="44788b34-78e3-3cf9-a98d-07cbb18f8fee" /mnt/dabou2 hfsplus defaults 0 0
```
- To format the `NVMe` SSD disk and use `ext4` as format, execute these commands:
```bash
fdisk /dev/nvme0n1
mkfs.ext4 /dev/nvme0n1
```
**Remark**: Next, you should be able to get the UUID s edit the `/etc/fstab` file
- Mount the volumes
  ```bash
  mkdir -p /media/dabou
  mkdir -p /media/dabou2
  mount -a
  ```
- All in one steps
  ```bash
  UUID=$(blkid | sed -n '/nvme0n1/s/.*UUID=\"\([^\"]*\)\".*/\1/p')
  echo "UUID="${UUID}" /media/dabou ext4 defaults 0 0" >> /etc/fstab
  mkdir -p /media/dabou
  mount -a
  ```