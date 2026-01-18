## Overview of /home

The `/home` directory serves as the personal workspace for each normal user on the system.

### Structure Example

```
/home/
├── jeel
├── alice
└── bob
```

Your personal home directory (e.g., for user `jeel`):

```
/home/jeel
```

### What's Stored Here?

- Your personal files (documents, downloads, etc.)
- Shell configurations
- Editor settings
- Application preferences

### Key Linux Rule

- **System-wide configs** → `/etc`
- **User-specific configs** → `/home/username`

## Dotfiles (.\* Files and Directories)

Dotfiles are files or directories starting with a dot (`.`), which are hidden by default in tools like `ls`. Use `ls -a` to view them.

### Common Dotfiles

```
.bashrc
.profile
.gitconfig
.config/
.local/
.cache/
```

### Why the Dot?

- Historically, the dot indicates hidden files.
- They store configurations, not regular documents.

### Golden Rule for Dotfiles

- **They control environment behavior** (customizations, preferences, startup logic).
- **They do NOT** store programs, binaries, or libraries.

## Must-Know Dotfiles

### 1. .bashrc

- **Purpose**: Interactive shell configuration.
- **When Used**: Opening a terminal or starting an interactive shell.
- **Common Contents**:
  ```bash
  alias ll='ls -la'
  export PATH="$HOME/bin:$PATH"
  PS1='\u@\h:\w\$ '
  ```
- **Key Points**:
  - Runs every time you open a terminal.
  - Ideal for aliases, functions, and environment variables.

### 2. .profile

- **Purpose**: Login shell configuration.
- **When Used**: Logging in via TTY, SSH, or sometimes display managers.
- **Typical Contents**:
  ```bash
  export PATH="$HOME/.local/bin:$PATH"
  ```
- **Key Points**:
  - Runs once per login.
  - May source `.bashrc`.

### 3. .bash_profile

- **Purpose**: Bash-specific login config (used instead of `.profile` if present).
- **Rule**: `.bash_profile` takes precedence over `.profile`.
- Most modern distros prefer `.profile` for simplicity.

### 4. .config/ (Very Important)

- **Purpose**: Modern app configuration directory (follows XDG Base Directory Specification).
- **Structure**:
  ```
  .config/
  ├── nvim/
  ├── alacritty/
  ├── git/
  ├── tmux/
  └── vscode/
  ```
- **Example**:
  ```
  ~/.config/nvim/init.lua
  ```
- **Benefit**: Cleaner organization; each app has its own folder (replaces older dotfiles like `.vimrc`).

### 5. .local/

- **Purpose**: User-local data.
- **Common Subdirectories**:
  ```
  .local/
  ├── bin/     ← Personal executables
  ├── share/   ← App data
  └── state/
  ```
- **Importance**:
  - `~/.local/bin`: Install tools without root (e.g., `pip install --user black` places binaries here).
  - Keeps the system clean and allows personal tool management.

### 6. .cache/

- **Purpose**: Disposable data (thumbnails, compiled caches, temporary app data).
- **Safe to Delete**:
  ```bash
  rm -rf ~/.cache/*
  ```
- Apps will recreate it as needed.

### 7. .gitconfig

- **Purpose**: User-level Git configuration.
- **Example**:
  ```ini
  [user]
      name = Jeel
      email = jeel@example.com
  ```
- **Overrides**: System → Global → Repo.

### 8. .ssh/

- **Purpose**: SSH keys and config.
- **Structure**:
  ```
  .ssh/
  ├── id_ed25519
  ├── id_ed25519.pub
  ├── known_hosts
  └── config
  ```
- **Permissions (Critical)**:
  ```bash
  chmod 700 ~/.ssh
  chmod 600 ~/.ssh/id_ed25519
  ```

### 9. .vimrc, .tmux.conf, .inputrc

- **Purpose**: Tool-specific configs (older style).
- **Examples**:
  - `.vimrc`: Vim config.
  - `.tmux.conf`: tmux config.
  - `.inputrc`: Readline behavior.
- Many tools now prefer `~/.config/`, but these persist.

## How Linux Processes These Files

When opening a terminal:

1. **Login?** → `.profile` or `.bash_profile`.
2. **Interactive shell?** → `.bashrc`.
3. **App starts?** → Checks `~/.config/app/`.
4. **Command runs?** → Checks `$PATH`.

The process is deterministic and follows a logical order.

## Permissions in /home

Typical permissions:

```
drwxr-x--- jeel jeel /home/jeel
```

- **Meaning**:
  - You (owner): Full access.
  - Others: No access.
- **Protection**: Safeguards SSH keys, browser data, tokens, and configs.
