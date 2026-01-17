# Linux Permissions and Chmod

## Basic Permissions

Every file or directory in Linux has three permission sets:

- **Owner**: The user who owns the file.
- **Group**: The group associated with the file.
- **Others**: All other users.

Permissions are represented as `rwx` for each set (e.g., `rwxr-x---` means owner has full access, group has read and execute, others have none).

| Permission | Symbol | Meaning (File) | Meaning (Directory) |
| ---------- | ------ | -------------- | ------------------- |
| Read       | `r`    | Read file      | List files          |
| Write      | `w`    | Modify file    | Create/delete files |
| Execute    | `x`    | Run file       | Enter directory     |

## Using Chmod

There are two ways to use `chmod`:

1. **Symbolic Mode**
2. **Numeric Mode**

### Symbolic Mode

Syntax: `chmod [who][+|-|=][permissions] <file>`

- `+`: Add permission
- `-`: Remove permission
- `=`: Set exactly to these permissions

Who:

- `u`: Owner (user)
- `g`: Group
- `o`: Others
- `a`: All

Examples:

```bash
chmod u+x script.sh
chmod g-w report.txt
chmod u=rwx,g=rx,o= file.sh
```

### Numeric Mode

| Permission | Value |
| ---------- | ----- |
| `r`        | 4     |
| `w`        | 2     |
| `x`        | 1     |

| Number | Meaning       |
| ------ | ------------- |
| 7      | `rwx` (4+2+1) |
| 6      | `rw-`         |
| 5      | `r-x`         |
| 4      | `r--`         |
| 0      | `---`         |

Syntax: `chmod XYZ <file>`

- `X`: Owner
- `Y`: Group
- `Z`: Others

Examples:

```bash
chmod 750 deploy.sh  # Equivalent to rwxr-x---
chmod 644 readme.md  # Equivalent to rw-r--r--
chmod -R 770 project # Recursive (affects everything inside)
```

## Special Bits

Permissions can include special bits at the beginning: `[special][owner][group][others]`.

| Bit | Name   | Applies To          |
| --- | ------ | ------------------- |
| 4   | setuid | Files               |
| 2   | setgid | Files & directories |
| 1   | sticky | Directories         |

| Symbol | Meaning                       |
| ------ | ----------------------------- |
| `s`    | setuid or setgid + execute    |
| `S`    | setuid/setgid without execute |
| `t`    | sticky + execute              |
| `T`    | sticky without execute        |

| Feature                    | Sticky (1)          | Setgid (2)          |
| -------------------------- | ------------------- | ------------------- |
| Controls delete            | Yes                 | No                  |
| Controls group inheritance | No                  | Yes                 |
| Allows creation            | No (depends on `w`) | No (depends on `w`) |
| Affects owner              | No                  | No                  |

---

## Sticky Bit (1) — Delete Protection (Directories)

### What It Does

In a writable directory, users can delete only their own files, not others'.

It does **not**:

- Change owner
- Change group
- Allow creation by itself
- Override read/write permissions

### Real-Life Example: /tmp

```bash
ls -ld /tmp
```

Output:

```
drwxrwxrwt root root /tmp
```

- Everyone can create files (`rwx`).
- Sticky bit (`t`) prevents users from deleting others' files.

### Scenario: Shared Scratch Space

**Problem:** Multiple users share a directory, and anyone can delete anyone else's files.

**Solution:**

```bash
chmod 1777 /shared
```

**Result:**

- Users can create files.
- Users can delete only their own files.
- Directory owner can delete anything.

### When to Use Sticky Bit

- `/tmp`-like shared directories
- Public upload areas

### When Not to Use

- Team project directories (use setgid instead)

---

## Setgid (2) — Group Inheritance (Files & Directories)

### What It Does (Most Common Use: Directories)

New files and directories inherit the directory's group, not the user's primary group.

It does **not**:

- Change who owns the file (owner is always the creator).
- Allow creation by itself (needs `w`).

### Real-Life Example: Team Project Directory

**Setup:**

- Group: `devteam`
- Members: `jeel`, `chitu`, `alice`

```bash
chgrp devteam /srv/project
chmod 2770 /srv/project
```

Output:

```
drwxrws--- root devteam /srv/project
```

### What Happens Now

| Creator | Owner | Group   |
| ------- | ----- | ------- |
| jeel    | jeel  | devteam |
| chitu   | chitu | devteam |
| alice   | alice | devteam |

Perfect for collaboration: No more mixed groups.

### Setgid on Files (Rare)

Program runs with the file's group permissions. Used occasionally for shared tools, but uncommon today.

### When to Use Setgid

- Team/project directories
- Shared workspaces
- Lab machines

---

## Setuid (4) — Run as Owner (Files Only)

### What It Does

When an executable runs, it runs as the file's owner, not the user who launched it.

It does **not**:

- Ask for a password
- Change ownership
- Override execute permission

### Real-Life Example: passwd

```bash
ls -l /usr/bin/passwd
```

Output:

```
-rwsr-xr-x root root /usr/bin/passwd
```

- Owner: `root`
- Setuid enabled (`s`)
- Any user running `passwd` temporarily runs it as root.

**Why This Exists:**

- Passwords are stored in `/etc/shadow` (root-only).
- Users must change their own passwords.
- Setuid provides controlled privilege escalation.

### Numeric Example

```bash
chmod 4755 program
```

- 4 → setuid
- 7 → owner rwx
- 5 → group r-x
- 5 → others r-x

### Important Warning

- Setuid on custom programs is dangerous.
- Bugs can lead to full root compromise.
- Linux ignores setuid on scripts for safety.

Do **not** use setuid to avoid sharing sudo passwords.

### Correct Alternative (Best Practice)

Use sudo rules instead:

```bash
sudo visudo
```

Add:

```
%devteam ALL=(root) NOPASSWD: /usr/bin/systemctl restart myservice
```

Safe, auditable, and limited.

---

## Side-by-Side Comparison

| Bit        | Purpose           | Typical Use              |
| ---------- | ----------------- | ------------------------ |
| 1 (sticky) | Protect deletion  | /tmp, shared upload dirs |
| 2 (setgid) | Group inheritance | Team projects            |
| 4 (setuid) | Run as owner      | System binaries only     |

# letter at the beginning of the permissions

| Letter | Meaning           | Example               |
| ------ | ----------------- | --------------------- |
| `-`    | Regular file      | text file, binary     |
| `d`    | Directory         | `/etc`, `/home`       |
| `l`    | Symbolic link     | `stdout -> /proc/...` |
| `b`    | Block device      | `sda`, `sda1`         |
| `c`    | Character device  | `tty`, `tpm0`         |
| `p`    | Named pipe (FIFO) | IPC pipes             |
| `s`    | Socket            | Unix domain socket    |
