## HowTo mount a USB drive

See - https://gist.github.com/brysonian/958d760ff5e8c7aecc45d17c4a92b1c7

```bash
VBoxManage list usbhost | more
...
UUID:               7d2a6b8b-0de4-4f09-8bdd-5cdfffba3aa1
VendorId:           0x14cd (14CD)
ProductId:          0x1212 (1212)
Revision:           1.0 (0100)
Port:               3
USB version/speed:  0/High
Manufacturer:       Generic
Product:            Mass Storage Device
SerialNumber:       121220160204
Address:            p=0x1212;v=0x14cd;s=0x00000a5435d2d752;l=0x14323000
Current State:      Busy
```

- Edit the Vagrantfile to change the `usbfilter/vendorId and productId` 
```
vb.customize ['usbfilter', 'add', '0',
  '--target', :id,
  '--name', 'PiImage',
  '--vendorid', '0x05e3', <-----
  '--productid', '0x0736'] <-----
...
vb.customize ['usbfilter', 'add', '0',
  '--target', :id,
  '--name', 'DietPi',
  '--vendorid', '0x14cd',
  '--productid', '0x1212']      
```
- Once the machine is up, ssh to it or execute remotely the commands
  ```bash
  vagrant up
  vagrant ssh 
  ```
- To mount the device more easily, install the following tool:
  ```bash
  vagrant ssh -c "sudo apt-get install pmount"
  ```
- Next, check where the disk has been mounted
  ```bash
  vagrant ssh -c "sudo fdisk -l"
  ```
- And mount it. This will mount the drive in `/media/sdb1`
  ```bash
  vagrant ssh -c "pmount /dev/sdb1"
  ```
- To unmount, execute this command `pumount /dev/sdb1`
- To copy the files, install the following plugin
  ```bash
  vagrant plugin install vagrant-scp
  ```
- Next copy the files from the host to the VM using this command
  ```bash
  vagrant scp ../config/dietpi-wifi.txt /media/sdb1/boot
  vagrant scp ../config/dietpi.txt /media/sdb1/boot
  ```