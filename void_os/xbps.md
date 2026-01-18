XBPS stands for X Binary Package System. It is Void Linux’s native package manager, written in C, and is simple, fast, and dependency-correct. It is split into client tools and libraries.

## Core Components

XBPS is a toolchain, not a single binary.

| Tool               | Purpose                    |
| ------------------ | -------------------------- |
| `xbps-install`     | Install / update packages  |
| `xbps-remove`      | Remove packages            |
| `xbps-query`       | Query package database     |
| `xbps-reconfigure` | Reconfigure packages       |
| `xbps-pkgdb`       | Fix package database       |
| `xbps-rindex`      | Repo index tool            |
| `xbps-create`      | Create packages            |
| `xbps-src`         | Build packages from source |

Mirror location: `/etc/xbps.d/00-repository-main.conf`

Use `--help` for detailed options on each tool.

## Commands

### 1. Sync Repository Index

```bash
sudo xbps-install -S
```

What it does:  
Downloads latest package metadata (like `apt update` or `pacman -Sy`).

When to use: Before installing or upgrading.

### 2. Full System Upgrade

```bash
sudo xbps-install -Su
```

What it does:  
`-S` syncs repos, `-u` upgrades all installed packages.

Daily use: This is the most common command.

### 3. Install a Package

```bash
sudo xbps-install vim
```

What it does:  
Installs `vim` and dependencies.

For multiple:

```bash
sudo xbps-install git gcc make
```

### 4. Install Without Confirmation

```bash
sudo xbps-install -y htop
```

Useful for: Scripts, automation, fast installs.

### 5. Search for a Package (Remote)

```bash
xbps-query -Rs python
```

What it shows:  
Packages containing `python` in name/description.

Example output:

```
python3-3.12.1_1 The Python programming language
python3-numpy-1.26.3_1 Numerical Python
```

### 6. List Installed Packages

```bash
xbps-query -l
```

Filter:

```bash
xbps-query -l | grep gcc
```

### 7. Show Info About a Package

```bash
xbps-query -S vim
```

Shows: Version, dependencies, license, size.

### 8. Remove a Package

```bash
sudo xbps-remove firefox
```

Removes only the package.

### 9. Remove Package + Unused Dependencies

```bash
sudo xbps-remove -R firefox
```

Preferred over plain remove.

### 10. Remove Orphaned Packages

```bash
sudo xbps-remove -o
```

What it does: Removes unused dependencies.

Run occasionally for cleanup.

### 11. Find Which Package Owns a File

```bash
xbps-query -o /usr/bin/gcc
```

Output example:

```
gcc-13.2.0_1
```

### 12. List Files Installed by a Package

```bash
xbps-query -f vim
```

Example:

```
/usr/bin/vim
/usr/share/vim/vimrc
```

### 13. Show Dependencies of a Package

```bash
xbps-query -x vim
```

Useful for: Understanding bloat, debugging issues.

### 14. Show Reverse Dependencies

```bash
xbps-query -X openssl
```

Use before removing core libraries.

### 15. Reconfigure a Package

```bash
sudo xbps-reconfigure openssl
```

Use when: Cert issues, fonts broken, locales broken, after upgrades.

### 16. Reconfigure Everything

```bash
sudo xbps-reconfigure -a
```

Good after: Big updates, system issues.

### 17. Check Package Database Health

```bash
sudo xbps-pkgdb -a
```

Detects: Broken packages, missing files.

### 18. Force-Fix a Broken Package

```bash
sudo xbps-pkgdb -f openssl
```

Use when something refuses to work.

### 19. Hold a Package

```bash
sudo xbps-pkgdb -m hold linux
```

Prevents upgrades (e.g., kernel pinning).

Unhold:

```bash
sudo xbps-pkgdb -m unhold linux
```

### 20. Install Local .xbps Package

```bash
sudo xbps-install ./mypkg-1.0_1.x86_64.xbps
```

Used with: `xbps-src`, custom builds.

### 21. Show Repositories in Use

```bash
xbps-query -L
```

### 22. Dry-Run an Install

```bash
sudo xbps-install -n firefox
```

Shows: What would be installed, size, dependencies (no changes).

### 23. Downgrade a Package

```bash
sudo xbps-install -f vim-9.0_1
```

If version exists in repo/cache.

### 24. Clean Cached Packages

```bash
sudo xbps-remove -O
```

Frees disk space.

### 25. Daily Maintenance

```bash
sudo xbps-install -Su
sudo xbps-remove -o
sudo xbps-reconfigure -a
```
