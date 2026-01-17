lsblk
list all the partitions connected to the system.

mount
linux doesn't auto-use disks.we have to mount them into the directory tree.
ex.
sudo mkdir /mnt/usb
sudo mount /dev/sdb1 /mnt/usb

umount to deatach a filesystem.
ex.
sudo umount /mnt/usb

df -h shows used & available spaces.(only for Mounted)
