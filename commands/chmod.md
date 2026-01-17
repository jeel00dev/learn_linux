every file has 3 permission sets
owner | group | others
| Permission | Symbol | Meaning (file) | Meaning (directory) |
| ---------- | ------ | -------------- | ------------------- |
| Read | `r` | read file | list files |
| Write | `w` | modify file | create/delete files |
| Execute | `x` | run file | enter directory |

- | rwx | r-x | ---
  owner group others

there are two ways to use chmod

1. symbolic mode
2. numeric mode

chmod [who][+|-|=][permissions] file

`+` means add - mean remove = mean set exactly same permission
Who
u → owner (user)
g → group
o → others
a → all

ex.
chmod u+x script.sh
chmod g-w report.txt
chmod u=rwx,g=rx,o= file.sh

2. numeric mode

| Permission | Value       |
| ---------- | ----------- |
| r          | 4           |
| w          | 2           |
| x          | 1           |
| Number     | Meaning     |
| ------     | ----------- |
| 7          | rwx (4+2+1) |
| 6          | rw-         |
| 5          | r-x         |
| 4          | r--         |
| 0          | ---         |

chmod XYZ file
X is owner
Y is group
Z is others

ex.
chmod 750 deploy.sh is similar to rwxr-x---
chmod 644 readme.md is similar to rw-r--r--
chmod -R 770 project recursive permission (affects everything inside)

there is also an special bits
[ special ][ owner ][ group ][ others ]
| Bit | Name | Applies to |
| --- | ------ | ------------------- |
| 4 | setuid | files |
| 2 | setgid | files & directories |
| 1 | sticky | directories |
| Symbol | Meaning |
| ------ | ----------------------------- |
| `s` | setuid or setgid + execute |
| `S` | setuid/setgid without execute |
| `t` | sticky + execute |
| `T` | sticky without execute |
| Feature | sticky (1) | setgid (2) |
| -------------------------- | ------------------ | ------------------ |
| Controls delete | ✅ | ❌ |
| Controls group inheritance | ❌ | ✅ |
| Allows creation | ❌ (depends on `w`) | ❌ (depends on `w`) |
| Affects owner | ❌ | ❌ |

````
---

## 1️⃣ Sticky Bit (`1`) — Delete Protection (Directories)

### What it does

> In a writable directory, **users can delete only their own files**, not others’.

It **does NOT**:

* change owner
* change group
* allow creation by itself
* override read/write permissions

---

### Real-life example: `/tmp`

```bash
ls -ld /tmp
````

```text
drwxrwxrwt  root root /tmp
```

- Everyone can create files (`rwx`)
- Sticky bit (`t`) prevents users from deleting others’ files

---

### Scenario (shared scratch space)

**Problem:**
Multiple users share a directory. Anyone can delete anyone else’s files.

**Solution:**

```bash
chmod 1777 /shared
```

**Result:**

- Users can create files
- Users can delete **only their own** files
- Directory owner can delete anything

---

### When to use sticky bit

- `/tmp`-like shared directories
- Public upload areas

### When NOT to use

- Team project directories (use setgid instead)

---

## 2️⃣ setgid (`2`) — Group Inheritance (Files & Directories)

### What it does (most common use: directories)

> New files and directories **inherit the directory’s group**, not the user’s primary group.

It **does NOT**:

- change who owns the file (owner is always the creator)
- allow creation by itself (needs `w`)

---

### Real-life example: Team project directory

**Setup:**

- Group: `devteam`
- Members: `jeel`, `chitu`, `alice`

```bash
chgrp devteam /srv/project
chmod 2770 /srv/project
```

```text
drwxrws--- root devteam /srv/project
```

---

### What happens now

| Creator | Owner | Group   |
| ------- | ----- | ------- |
| jeel    | jeel  | devteam |
| chitu   | chitu | devteam |
| alice   | alice | devteam |

🔥 **Perfect collaboration**
No more mixed groups like `jeel`, `chitu`, etc.

---

### setgid on files (rare)

> Program runs with the file’s **group permissions**.

Used occasionally for shared tools, but uncommon today.

---

### When to use setgid

- Team/project directories
- Shared workspaces
- Lab machines

---

## 4️⃣ setuid (`4`) — Run as Owner (Files Only)

### What it does

> When an executable runs, it runs **as the file’s owner**, not the user who launched it.

It **does NOT**:

- ask for a password
- change ownership
- override execute permission

---

### Real-life example: `passwd`

```bash
ls -l /usr/bin/passwd
```

```text
-rwsr-xr-x  root root /usr/bin/passwd
```

- Owner: `root`
- setuid enabled (`s`)
- Any user running `passwd` temporarily runs it **as root**

**Why this exists:**

- Passwords are stored in `/etc/shadow` (root-only)
- Users must change their own passwords
- setuid provides **controlled privilege escalation**

---

### Numeric example

```bash
chmod 4755 program
```

```
4 → setuid
7 → owner rwx
5 → group r-x
5 → others r-x
```

---

### ⚠️ VERY IMPORTANT WARNING

- setuid on custom programs is **dangerous**
- Bugs = full root compromise
- Linux ignores setuid on scripts for safety

👉 **Do NOT use setuid to avoid sharing sudo passwords**

---

### Correct alternative (best practice)

Use **sudo rules** instead:

```bash
sudo visudo
```

```text
%devteam ALL=(root) NOPASSWD: /usr/bin/systemctl restart myservice
```

Safe, auditable, and limited.

---

## Side-by-side comparison

| Bit        | Purpose           | Typical Use                |
| ---------- | ----------------- | -------------------------- |
| 1 (sticky) | protect deletion  | `/tmp`, shared upload dirs |
| 2 (setgid) | group inheritance | team projects              |
| 4 (setuid) | run as owner      | system binaries only       |

---
