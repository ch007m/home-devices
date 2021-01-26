## Format a disk using FAT64 (= exfat)

- [Formating a extFAT HD](https://matthew.komputerwiz.net/2015/12/13/formatting-universal-drive.html)
- [How to partition using different tools](https://stackpointer.io/unix/linux-script-to-partition-disk/632/)
- [Remove Hard Disk data](https://how-to.fandom.com/wiki/How_to_wipe_a_hard_drive_clean_in_Linux)

- To clean the hard disk, umount it first
  ```bash
  umount /dev/sda -l
  ```
- Next execute `wipefs -a /dev/sda` to start from a fresh disk or use `dd if=/dev/zero of=/dev/sda bs=512 count=1 status=progress`
- Partition the HD
```bash
```
- Format it
```bash

```

### Automate the partitioning of the disk

- Automate the creation of the partition on the disk
```bash
Simply create a file (e.g. fdisk_cmds) that contains the commands, as for instance:

n
1
    # This is a blank line to select a default response
q

fdisk /dev/sda < fdisk_cmds

or

(echo p; echo d; ... ; echo w) | fdisk /dev/sda
```

- Get the disk partition definition and reuse it to format the disk
```bash
sfdisk --dump /dev/sda > sda.dump && cat sda.dump
label: gpt
label-id: 9F9ECBFF-1114-4333-846B-61CF8CFE12B1
device: /dev/sda
unit: sectors
first-lba: 34
last-lba: 3906963422

/dev/sda1 : start=          40, size=      409600, type=C12A7328-F81F-11D2-BA4B-00A0C93EC93B, uuid=4C30D8D9-0D24-4D72-B997-26260F56EA69, name="EFI System Partition"
/dev/sda2 : start=      411648, size=  3906549760, type=EBD0A0A2-B9E5-4433-87C0-68B6B72699C7, uuid=83CC6B88-E1D7-422F-B446-5B51947B87BD

cat sda.dump | sudo sfdisk /dev/sda
```

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
  find /media/dabou/ -type d -exec chmod 755 {} \;
  find /media/dabou/ -type f -exec chmod 644 {} \;
  mount -a
  ```
## Copy Hard disk to another

They are different strategies to backup a hard disk to another such as using `dd`
```bash
dd if=/dev/nvme0n1 of=/dev/sda
```
or `rsync` - see [tuto](http://doc.ubuntu-fr.org/rsync). To copy all the content created under the source directory, then append `/` end of the path
```bash
rsync -r -avhP --exclude={'._.DS_Store','.DS_Store','._*'} /media/dabou/ /media/dabou2/
```

## Schedule backup every day

See documentation [here](https://www.howtogeek.com/135533/how-to-use-rsync-to-backup-your-data-on-linux/)

- Edit the `crontab` using `crontab -e`
- Include the command to back up every night the content of the source directory (e.g. NMVe disk) to the external SB disk
```bash
0 22 * * * rsync -r -avhP --exclude={'._.DS_Store','.DS_Store','._*'} /media/dabou/ /media/dabou2/
```
