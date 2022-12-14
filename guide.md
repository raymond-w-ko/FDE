
# Full Disk Encrypt Guide

## Create a keyfile
```
dd if=/dev/urandom bs=1M count=8 2>/dev/null | openssl aes-256-cbc -pbkdf2 -out disk.enc
openssl aes-256-cbc -pbkdf2 -d -in disk.enc -out disk.key
```

## Clean disk
```
wipefs --all --backup /dev/sdX
```

## Encrypt the disk
```
cryptsetup --cipher=aes-xts-plain64 --key-size=256 --offset=0 --key-file=./disk.key open --type=plain /dev/sdX enc
```

## Create partitions with LVM
```
pvcreate /dev/mapper/enc
vgcreate store /dev/mapper/enc
lvcreate -L 12G store -n swap
lvcreate -l 100%FREE store -n home
```

## After normal install

### In mkinitcpio.conf
```
HOOKS=(... keyboard block ssldec encrypt lvm2 filesystems fsck)
```
### Install ssldec to initcpio
```
pacman -S wget
cd /lib/initcpio/install/
wget .../install/ssldec
cd ../hooks/
wget .../hooks/ssldec
```

### Create a bootable USB drive
* `wipefs` on the USB drive.
* Use `cfdisk` to create a `dos` partition table, with one partition, FAT32, 0xb partition type code.
* `grub-install --target=i386-pc --recheck --boot-directory=/mnt/bootusb /dev/sdX`
* (above not in chroot)
* mount USB drive
* chroot
* `cd /boot`
* `cp -r * /boot.usb/`
