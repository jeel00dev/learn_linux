# Complete Linux Directory Structure (Single View)

```
/
├── bin -> usr/bin                  # soft link (essential user commands)
├── sbin -> usr/sbin                # soft link (system binaries)
├── lib -> usr/lib                  # soft link (shared libraries)
├── lib64 -> usr/lib64              # soft link (64-bit libraries)
│
├── boot/                           # bootloader and kernel
│   ├── vmlinuz
│   └── initramfs.img
│
├── dev/                            # device files
│   ├── sda
│   ├── sda1
│   ├── tty
│   └── null
│
├── etc/                            # system configuration
│   ├── passwd
│   ├── shadow
│   ├── fstab
│   └── ssh/
│
├── home/                           # user home directories
│   └── jeel/
│       ├── Documents/
│       ├── Downloads/
│       └── notes.txt
│
├── root/                           # root user home
│
├── media/                          # auto-mounted devices
│   └── usb/
│
├── mnt/                            # manual mount points
│
├── opt/                            # optional third-party software
│   └── vscode/
│
├── proc/                           # process & kernel info (virtual)
│   ├── cpuinfo
│   ├── meminfo
│   └── 1/
│
├── sys/                            # kernel objects (virtual)
│   └── class/
│       └── net/
│
├── run/                            # runtime process data
│
├── tmp/                            # temporary files
│
├── usr/                            # user programs and data
│   ├── bin/
│   │   ├── gcc
│   │   ├── python3
│   │   └── vim
│   ├── sbin/
│   ├── lib/
│   └── share/
│
├── var/                            # variable data
│   ├── log/
│   │   ├── syslog
│   │   └── auth.log
│   ├── cache/
│   └── spool/
│
├── fileA                           # original file
├── fileB                           # hard link to fileA (same inode)
└── link_to_fileA -> fileA          # soft link to fileA
```

## The Root Directory (/)

- **The starting point** of the entire filesystem.
- Every file, directory, device, and link exists under `/`.
- You cannot boot Linux without it.

Think of `/` as:  
**“The universe of the Linux system”**

## /bin → /usr/bin (soft link)

### Purpose

- Essential user commands needed for basic system usage.

### Examples

```bash
ls, cp, mv, cat, echo, bash
```

### Why it’s a symlink

- Modern Linux follows the /usr merge.
- `/bin` exists for backward compatibility.
- Actual binaries live in `/usr/bin`.

### Important

- Required even in single-user / rescue mode.

## /sbin → /usr/sbin (soft link)

### Purpose

- System administration commands.
- Mostly used by root.

### Examples

```bash
mount, umount, reboot, fsck, ip
```

### Key Point

- Regular users usually don’t need `/sbin`.
- Still essential for boot and recovery.

## /lib & /lib64 → /usr/lib\* (soft links)

### Purpose

- Shared libraries required by `/bin` and `/sbin`.

### Examples

```text
libc.so
ld-linux-x86-64.so
```

### Why Critical

- If libraries are missing → commands won’t run.
- Kernel loads these during early boot.

## /boot

### Purpose

- Files required to boot the system.

### Contains

```text
vmlinuz        → Linux kernel
initramfs     → temporary root filesystem
grub/          → bootloader config
```

### Key Idea

- BIOS/UEFI loads files from `/boot`.
- Small but extremely important directory.

## /dev

### Purpose

- Represents hardware as files.

### Examples

```text
/dev/sda      → hard disk
/dev/tty      → terminal
/dev/null     → data black hole
```

### Important Concept

- These are device nodes, not real files.
- Created dynamically by udev.

Linux philosophy:  
**“Everything is a file”**

## /etc

### Purpose

- System-wide configuration files.

### Examples

```text
/etc/passwd   → user info
/etc/fstab    → mount configuration
/etc/ssh/     → SSH settings
```

### Rules

- Mostly text files.
- No binaries here.
- Editing `/etc` = changing system behavior.

## /home

### Purpose

- Personal space for users.

### Structure

```text
/home/jeel/
```

### Contains

- Documents
- Downloads
- Configs (~/.bashrc, ~/.vimrc)

### Key Point

- Safe to back up.
- Deleting `/home` ≠ breaking system.

## /root

### Purpose

- Home directory for the root user.

### Why Separate from /home

- Ensures root can log in even if `/home` isn’t mounted.
- Security + reliability.

## /media

### Purpose

- Auto-mounted removable devices.

### Examples

```text
/media/usb
/media/jeel/USB_DRIVE
```

### Used By

- Desktop environments
- USB drives
- External disks

## /mnt

### Purpose

- Manual mount point.

### Used By

```bash
mount /dev/sdb1 /mnt
```

### Difference from /media

- `/media` → automatic
- `/mnt` → manual, temporary

## /opt

### Purpose

- Optional / third-party software.

### Examples

```text
/opt/google/
/opt/vscode/
```

### Why It Exists

- Keeps non-distro software isolated.
- Cleaner system management.

## /proc (virtual filesystem)

### Purpose

- Kernel + process information.

### Examples

```text
/proc/cpuinfo
/proc/meminfo
/proc/1234/
```

### Key Facts

- No real files on disk.
- Generated by kernel in memory.
- Used heavily by system tools.

## /sys (virtual filesystem)

### Purpose

- Interface to kernel objects and drivers.

### Examples

```text
/sys/class/net/
/sys/block/
```

### Used For

- Hardware introspection
- Power management
- Driver communication

More structured than `/proc`.

## /run

### Purpose

- Runtime data since boot.

### Examples

```text
/run/systemd/
/run/lock/
```

### Properties

- Stored in RAM.
- Cleared on reboot.
- Replaced older `/var/run`.

## /tmp

### Purpose

- Temporary files.

### Properties

- Writable by all users.
- Often cleared on reboot.

### Used By

- Applications
- Shell scripts
- Compilers

## /usr

### Purpose

- Main userland software directory.

### Contains

```text
/usr/bin     → user commands
/usr/lib     → libraries
/usr/share   → architecture-independent data
```

### Important

- Most programs live here.
- Read-only on some systems.
- Central to modern Linux.

## /var

### Purpose

- Variable, changing data.

### Examples

```text
/var/log     → logs
/var/cache   → cached files
/var/spool   → mail, print queues
```

### Why Separate

- Grows over time.
- Prevents root filesystem overflow.

## Hard Link (concept in filesystem)

```text
fileA
fileB   → same inode as fileA
```

### Meaning

- Two names, one data block.
- Deleting one does NOT remove data.
- Data removed only when last link is deleted.

## Soft Link (symbolic link)

```text
link_to_fileA -> fileA
```

### Meaning

- Stores path to target.
- Breaks if target is removed.
- Can link directories and cross filesystems.

## short note

- `/` → root of everything
- `/bin`, `/sbin`, `/lib` → boot-critical
- `/usr` → actual software
- `/etc` → configuration
- `/proc`, `/sys` → kernel views
- Hard link → same inode
- Soft link → path reference
