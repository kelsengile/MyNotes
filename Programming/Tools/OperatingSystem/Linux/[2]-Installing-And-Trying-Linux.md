[Previous](./[1]-What-Is-Linux.md) | [Table of Contents](./[0]-Introduction-to-Linux.md) | [Next](./[3]-The-Linux-File-System.md)

*Getting Started*

# Lesson 2 - Installing And Trying Linux

## 2.1 Live USBs And Virtual Machines

Before committing to an install, Linux offers two low-risk ways to try a distribution:

- **Live USB** — a bootable USB drive containing the full OS, running entirely from the USB (or loaded into RAM) without touching your computer's hard drive. Created using tools like Rufus (Windows), balenaEtcher (cross-platform), or `dd` (Linux/macOS).
- **Virtual Machine (VM)** — software (VirtualBox, VMware, or UTM on Apple Silicon) that emulates an entire computer inside a window on your existing OS, letting Linux run as a "guest" alongside your normal desktop.

```
Your Computer (Host OS: Windows/macOS)
 ┌───────────────────────────────────┐
 │   VirtualBox / VMware                │
 │   ┌─────────────────────────┐     │
 │   │   Ubuntu (Guest OS)        │     │
 │   │   - own virtual disk        │     │
 │   │   - own virtual RAM/CPU     │     │
 │   └─────────────────────────┘     │
 └───────────────────────────────────┘
```

| | Live USB | Virtual Machine |
|---|---|---|
| Performance | Native (limited by USB read speed) | Slower (emulation overhead) |
| Persistence | Changes usually lost on reboot (unless configured) | Fully persistent, like a real install |
| Risk to existing OS | None (doesn't touch internal disk by default) | None (fully isolated) |
| Best for | Quick look, hardware compatibility testing | Extended use, development, testing configs |

---

## 2.2 Dual-Booting vs A Full Install

Once ready for a more permanent setup, there are two common approaches:

- **Full install** — Linux becomes the only operating system on the machine, using the entire disk. Simplest to set up and maintain, but removes access to your previous OS on that machine.
- **Dual-boot** — Linux is installed alongside an existing OS (commonly Windows) on separate disk partitions, with a boot menu (like GRUB) letting you choose which OS to start at power-on.

```
Disk
 ┌─────────────────┬─────────────────┐
 │  Windows Partition │  Linux Partition   │
 │  (NTFS)             │  (ext4)             │
 └─────────────────┴─────────────────┘
          ▲
    GRUB Boot Menu:
     1) Windows
     2) Ubuntu
```

Dual-booting requires shrinking an existing partition to make room and carries a small risk of data loss if the partitioning step is rushed — **always back up important files before repartitioning a disk**, regardless of how routine the process usually is.

---

## 2.3 The Installation Process

Most modern distributions (Ubuntu in particular) have streamlined graphical installers. The general flow:

1. **Boot from the Live USB** and choose "Install [Distro]" from the desktop or boot menu.
2. **Select language and keyboard layout.**
3. **Choose installation type** — "Erase disk and install" (full install), "Install alongside" (automatic dual-boot setup), or "Something else" (manual partitioning, for advanced setups).
4. **Set up partitions** (if manual) — typically at minimum a root partition (`/`) and a swap partition/file for virtual memory; a separate `/home` partition is a common choice so a future reinstall can preserve personal files.
5. **Create a user account** — including the username and password that will also be used with `sudo` (Lesson 6.2).
6. **Wait for installation and reboot** — the installer copies files, configures the bootloader, and prompts you to remove the USB before restarting.

Server-oriented distributions (Ubuntu Server, Debian netinst) typically use a text-based installer instead of a graphical one, following the same conceptual steps without a mouse-driven interface.

---

## 2.4 WSL (Windows Subsystem For Linux) As An Alternative

**WSL** lets Windows run a real Linux distribution directly, without a traditional VM or dual-boot, by installing it as an optional Windows feature.

```
wsl --install -d Ubuntu
```

- **WSL 2** (the current version) runs a real Linux kernel in a lightweight, fast-booting virtual machine that's tightly integrated with Windows — files can be accessed from both sides, and Linux command-line tools run at near-native speed.
- **Common uses** — running Linux-only development tools, Docker (which itself relies on Linux containers under the hood), and testing shell scripts meant for Linux servers, all without leaving Windows.
- **Limitations** — no full desktop environment by default (though GUI Linux apps can be run via WSLg on recent Windows versions), and it's not a substitute for learning Linux system administration on bare metal or a proper VM.

For many developers, WSL has become the most common entry point into daily Linux command-line use, since it requires no dedicated partition, dual-boot menu, or separate physical machine — just an optional feature turned on within an existing Windows install.

[Previous](./[1]-What-Is-Linux.md) | [Table of Contents](./[0]-Introduction-to-Linux.md) | [Next](./[3]-The-Linux-File-System.md)
