# Disk Management Commands

## lsblk

Lists all the partitions connected to the system.

Example:

```bash
lsblk
```

## mount

Linux does not automatically use disks; you have to mount them into the directory tree.

Example:

```bash
sudo mkdir /mnt/usb
sudo mount /dev/sdb1 /mnt/usb
```

## umount

Detaches a filesystem.

Example:

```bash
sudo umount /mnt/usb
```

## df

Shows used and available space (only for mounted filesystems). Use `-h` for human-readable format.

Example:

```bash
df -h
```
