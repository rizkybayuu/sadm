# sadm — Sadaptive Mount Manager

> **v1.0 — The Keystone Release**
>
> Universal, production-ready mount manager wrapper for Linux.
> Engineered with atomic batch session updates, smart process detection,
> dynamic auto-parenting, and an intuitive color-coded Text User Interface (TUI).

---

## Table of Contents

- [Features](#features)
- [Requirements](#requirements)
- [Installation](#installation)
- [Usage](#usage)
- [Examples](#examples)
- [Session Memory & State](#session-memory--state)
- [Repository Structure](#repository-structure)
- [Contributing](#contributing)
- [License](#license)
- [Credits](#credits)

---

## Features

- **Smart Auto-Parenting** — Automatically detects disks and partitions, mounting them into logical hierarchical directories
- **Complete Session Management** — Isolate mounts into grouped sessions; list, restore, and interact with specific session IDs
- **Atomic Batch Updates** — Reorganize and renumber session directories safely using native `mount --move` without triggering VFS deadlocks
- **Smart Process Detection (`-c`)** — Integrates `fuser` to deeply inspect mounts; operations are automatically aborted if devices are locked by active I/O. Can be combined dynamically (e.g., `sadm -c -m sda`)
- **Multi-Operator Parsing** — Filter and execute operations on multiple sessions using logical expressions (`=N`, `>N`, `<N`, `>=N`, `<=N`, `!=N`, or comma-separated lists)
- **Advanced Synchronization (`--sync`)** — Safely relocate unmanaged/external mounts to a dedicated workspace (`/sadm/ns`) with automatic backup and restore
- **Bind Mount Forcing (`-f`)** — Intelligently utilize `mount --bind` to allow multiple mountpoints for the same device without storing force states
- **Safety Locks** — Built-in protection against accidental unmounting of critical system partitions (e.g., `/`, `/boot/efi`)
- **Clean TUI** — Grouped, color-coded terminal output displaying filesystems (FS) and organized categories (System, External, Non-Session, Session)
- **Auto-Dependency Resolution** — Seamlessly integrates with `sadp` to fetch and install missing core utilities on the fly

---

## Requirements

| Requirement | Notes |
|---|---|
| Linux (any distro) | Standard distribution |
| Bash | Version 3.2 or newer |
| Root access | via `sudo` |
| Core utilities | `lsblk`, `findmnt`, `blkid`, `fuser`, `awk`, `sed`, `grep`, `xargs`, `sort`, `cut`, `tr`, `head`, `tail`, `mount`, `umount`, `curl` |

> Missing utilities are auto-installed via `sadp` if it is present on the system.

---

## Installation

```bash
sudo curl -L -o /usr/local/sbin/sadm \
  https://raw.githubusercontent.com/rizkybayuu/sadm/main/sadm \
  && sudo chmod +x /usr/local/sbin/sadm
```

---

## Usage

```
sadm -m <dev...> [-f] [--s]          Mount devices (auto-parenting) into a session
sadm -um <dev|path> [-l]             Unmount specific device or path (lazy optional)
sadm -c <target>                     Check busy processes locking a mountpoint
sadm --list [-a]                     Display colored, grouped mount list
sadm --sync                          Relocate external mounts to /sadm/ns
sadm --usync                         Restore mounts from sync backup
sadm --session-update                Atomic batch renumbering of active sessions
sadm --session-list                  Display all stored device lists and FS from memory
sadm --s-restore=<ID> [-f]           Restore session from memory (-f to force active mounts)
```

---

## Examples

```bash
# Mount devices into a new session
sadm -m sda sdb --session

# Force mount (bind) to a custom directory
sadm -mount -force sda1 --target /mnt/MyData

# Check activity on session 7
sadm --check --session=7

# Display all device lists saved in memory
sadm --session-list

# Sequential operation
sadm -mount sdc1 -umount -lazy sdb2

# Unmount all sessions except ID 1 and 2
sadm -umount --session!=1,2

# Batch mount everything unmounted into a new session
sadm -m -all --s

# Check before mounting
sadm -c sda1 -m sda1

# Check multiple devices then mount
sadm -c -m sda1 sda2
```

---

## Session Memory & State

Unlike standard scripts, `sadm` is **declarative**. Session configurations are stored securely in:

```
/var/lib/sadm/sessions/s_<ID>.list
```

The memory strictly tracks **Device | Mountpoint**. Method execution flags like `-f` (force) are intentionally **not** recorded — they are evaluated dynamically at runtime to prevent Ghost Mounts or conflicts upon system reboot.

Sync backups are stored at `/var/lib/sadm/sync_backup`.

> **Note:** The state directory (`/var/lib/sadm`) must reside on a local read-write filesystem. Base mount operations occur under `/sadm` unless modified via `--target`. NTFS partitions are handled natively using the modern `ntfs3` driver.

---

## Repository Structure

```
sadm/
├── README.md     # This file
├── LICENSE       # MIT License
└── sadm          # Main executable script
```

---

## Contributing

Contributions are welcome! Please open an issue or submit a pull request.
Ensure your code follows the existing style and has been tested safely on a live Linux VFS environment.

---

## Report Issues

[https://github.com/rizkybayuu/sadm/issues](https://github.com/rizkybayuu/sadm/issues)

---

## License

MIT License — Copyright © 2026 [rizkybayuu\_](https://github.com/rizkybayuu)
See [LICENSE](./LICENSE) for details.

---

## Credits

**Author:** [rizkybayuu\_](https://github.com/rizkybayuu)

Special thanks to the AI assistants that helped refine, audit, and harden the core architecture of this project:

- [Gemini](https://gemini.google.com) (Google)
- [Claude](https://claude.ai) (Anthropic)
- [DeepSeek](https://www.deepseek.com)
