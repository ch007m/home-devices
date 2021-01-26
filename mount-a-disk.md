## Format a disk using FAT64 (= exfat)

- [Formating a extFAT HD](https://matthew.komputerwiz.net/2015/12/13/formatting-universal-drive.html)
- [How to partition using different tools](https://stackpointer.io/unix/linux-script-to-partition-disk/632/)
- [Remove Hard Disk data](https://how-to.fandom.com/wiki/How_to_wipe_a_hard_drive_clean_in_Linux)

- To clean the hard disk, umount it first
  ```bash
  umount /dev/sda -l
  ```
- Next execute `wipefs -a /dev/sda` to start from a fresh disk or use `dd if=/dev/zero of=/dev/sda bs=512 count=1 status=progress`
- Partition the HD nd format it
```bash
echo "Writing zeros to partition: /dev/sda1"
dd if=/dev/zero of=/dev/sda1 bs=4K count=10

echo "Writing zeros to partition table: /dev/sda"
dd if=/dev/zero of=/dev/sda bs=4K count=1337

echo "Creating partition table, with target type: gpt"
parted -s /dev/sda mklabel gpt
parted -s /dev/sda mkpart primary 0% 100%

echo "Format the disk using exFAT format type"
mkfs.exfat /dev/sda1

echo "Create mount point"
mkdir -p /mnt/A2CB-3C1B
/dev/sda1 /mnt/A2CB-3C1B
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
label-id: BC5F9147-3F21-4F0B-A285-DE866297DA43
device: /dev/sda
unit: sectors
first-lba: 34
last-lba: 3906963422

/dev/sda1 : start=        2048, size=  3906959360, type=0FC63DAF-8483-4772-8E79-3D69D8477DE4, uuid=00B515BC-DEA9-490B-A350-CB77A7971F02, name="primary"
```
- Format the disk using the partition cfg
```bash
cat config/sda.cfg | sudo sfdisk /dev/sda
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
- For an `exFAT` formatted disk, add the `uid` and `groupid` of the user as you cannot change the ownership using the following command `chown -R ....` - see [discussion](https://stackoverflow.com/questions/25559700/chown-command-returning-operation-not-permitted)
  ```bash
  UUID=A2CB-3C1B /mnt/A2CB-3C1B exfat noatime,lazytime,rw,nofail,noauto,x-systemd.automount,gid=1000,uid=1000
  ```
- To format the `NVMe` SSD disk and use `ext4` as format, execute these commands:
```bash
fdisk /dev/nvme0n1
mkfs.ext4 /dev/nvme0n1
```
**Remark**: Next, you should be able to get the UUID

- Mount the volumes
  ```bash
  mount -a
  ```
- All in one steps
  ```bash
  UUID=$(blkid | sed -n '/nvme0n1/s/.*UUID=\"\([^\"]*\)\".*/\1/p')
  echo "UUID="${UUID}" /media/dabou ext4 noatime,lazytime,rw,nofail,noauto,x-systemd.automount" >> /etc/fstab
  mkdir -p /media/dabou
  find /media/dabou/ -type d -exec chmod 755 {} \;
  find /media/dabou/ -type f -exec chmod 644 {} \;
  mount -a
  ```
## Copy Hard disk to another

They are different strategies to back up a hard disk to another such as using `dd`
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
