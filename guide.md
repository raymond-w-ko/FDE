
# Full Disk Encrypt Guide

## Create a keyfile
```
dd if=/dev/urandom bs=1024 count=8 2>/dev/null | openssl aes-256-cbc -out disk.enc
openssl aes-256-cbc -d -in disk.enc -out disk.key
```

## Encrypt the disk
```
cryptsetup --cipher=aes-xts-plain64 --key-size=512 --offset=0 --key-file=./disk.key open --type=plain /dev/sdX encrypted-root
```

## Create partitions
```
cfdisk /dev/mapper/encrypted-root
partprobe /dev/mapper/encrypted-root
```

## After normal install

### In mkinitcpio.conf
```
HOOKS="... keyboard block ssldec encrypt lvm2 ... filesystems ..."
```
### Install ssldec to initcpio
```
pacman -S wget
cd /lib/initcpio/install/
wget .../install/ssldec
cd ../hooks/
wget .../hooks/ssldec
```
