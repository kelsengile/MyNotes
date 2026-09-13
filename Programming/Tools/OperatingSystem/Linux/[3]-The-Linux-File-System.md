[Previous](./[2]-Installing-And-Trying-Linux.md) | [Table of Contents](./[0]-Introduction-to-Linux.md) | [Next](./[4]-The-Command-Line-And-Shell.md)

*The Command Line*

# Lesson 3 - The Linux File System

## 3.1 The Directory Structure (/, /home, /etc, /var...)

Unlike Windows' separate drive letters (`C:\`, `D:\`), Linux organizes everything under a single root directory, `/`, with every drive and device mounted somewhere inside that one tree.

```
/
├── bin       → essential command binaries (ls, cp, bash)
├── boot      → bootloader files, the kernel itself
├── dev       → device files (disks, USB ports, represented as files)
├── etc       → system-wide configuration files
├── home      → personal directories, one per user (/home/alice)
├── lib       → shared libraries needed by binaries in /bin and /sbin
├── media     → auto-mounted removable media (USB drives, CDs)
├── mnt       → temporary manual mount point for filesystems
├── opt       → optional/third-party software packages
├── proc      → virtual filesystem exposing running process info
├── root      → the root user's home directory (not the same as /)
├── tmp       → temporary files, often cleared on reboot
├── usr       → user-installed programs and their resources
└── var       → variable data: logs, caches, spool files
```

A few directories worth knowing in more depth:

- **`/etc`** — nearly every system-wide setting lives here as plain text files (e.g. `/etc/hostname`, `/etc/ssh/sshd_config`), which is why "just edit a config file" is such a common troubleshooting step on Linux.
- **`/var/log`** — the default location for system and application logs, the first place to check when diagnosing a crash or unexpected behavior.
- **`/proc`** — not real files on disk; the kernel generates this content live, giving programs and curious users a window into running processes and system state (e.g. `cat /proc/cpuinfo`).

---

## 3.2 File Permissions And Ownership

Every file and directory has an **owner**, a **group**, and a set of **permissions** controlling who can read, write, or execute it, shown by `ls -l`:

```
$ ls -l notes.txt
-rw-r--r-- 1 alice staff 1024 Sep 10 14:02 notes.txt
```

```
-rw-r--r--
│└┬┘└┬┘└┬┘
│ │  │  └── Other: read only
│ │  └───── Group: read only
│ └──────── Owner (alice): read and write
└────────── File type (- = regular file, d = directory)
```

- **`r` (read)**, **`w` (write)**, **`x` (execute)** — the three permission types, each applying separately to the owner, the group, and everyone else ("other").
- **`chmod`** — changes permissions, either symbolically (`chmod u+x script.sh` adds execute for the owner) or numerically (`chmod 755 script.sh`, where each digit sums read=4, write=2, execute=1 for owner/group/other respectively).
- **`chown`** — changes a file's owner and/or group (e.g. `chown alice:staff notes.txt`), typically requiring `sudo` unless you already own the file.

```
chmod 755 script.sh
        │││
        │││
   owner(7=rwx) group(5=r-x) other(5=r-x)
```

---

## 3.3 Hidden Files And Configuration

Any file or directory whose name starts with a dot (`.`) is treated as **hidden**, invisible to a plain `ls` but shown with `ls -a`.

```
$ ls -a ~
.  ..  .bashrc  .config  .ssh  Documents  Downloads
```

- **Dotfiles** — a long-standing convention for storing personal configuration in the home directory, such as `.bashrc` (shell startup configuration), `.gitconfig` (Git settings), and `.ssh` (SSH keys and known-hosts).
- **`.config`** — a more modern, standardized location (the XDG Base Directory spec) where many newer applications store their settings instead of a top-level dotfile, keeping the home directory tidier.
- **"Dotfile management"** — many developers keep their dotfiles in a personal Git repository so a new machine's shell, editor, and tool configuration can be restored quickly by cloning it and symlinking the files into place.

---

## 3.4 Mounting Drives And Filesystems

**Mounting** is the process of attaching a filesystem (a hard drive, USB stick, network share, or disk image) to a specific point in the directory tree, making its contents accessible as if they were just another folder.

```
$ lsblk
NAME   SIZE  MOUNTPOINT
sda      500G
├─sda1    1G  /boot
└─sda2  499G  /
sdb      32G
└─sdb1   32G  /media/alice/USB_DRIVE
```

- **`mount`** — attaches a filesystem manually, e.g. `sudo mount /dev/sdb1 /mnt/usb`, making the USB drive's contents available under `/mnt/usb`.
- **`umount`** — detaches ("unmounts") a filesystem, which should always be done before physically removing a USB drive to avoid data corruption from writes still in progress.
- **`/etc/fstab`** — a configuration file listing filesystems that should be mounted automatically at boot, specifying the device, mount point, filesystem type, and mount options for each.
- **Desktop environments auto-mount** — plugging in a USB drive on a graphical Linux desktop typically mounts it automatically under `/media/<username>/<label>`, hiding the manual `mount` step entirely for everyday use.

[Previous](./[2]-Installing-And-Trying-Linux.md) | [Table of Contents](./[0]-Introduction-to-Linux.md) | [Next](./[4]-The-Command-Line-And-Shell.md)
