[⬅ Back to Operating Systems Fundamentals](../[0]-Introduction-to-OperatingSystems.md)

# Introduction to Linux

Linux is a free, open-source operating system kernel that, paired with a collection of surrounding software, forms complete operating systems called **distributions** (or "distros") — Ubuntu, Fedora, Debian, and Arch among the most common. Rather than being a single product controlled by one company, Linux is community-driven and endlessly customizable, which has made it the dominant operating system on servers, embedded devices, and much of the software development world, even though it holds a smaller share of everyday desktop computers.

This Topic covers what Linux actually is, how its file system and permissions model work, and the command-line skills that are central to using it effectively — package management, process control, and shell scripting among them. Desktop use is covered too, but the emphasis throughout is on the terminal, since that's where most of Linux's power and flexibility live.

## Why Learn Linux?

- **It runs the internet** — the overwhelming majority of web servers, cloud infrastructure, and supercomputers run Linux, making it essential knowledge for backend, DevOps, and systems work.
- **Free and fully customizable** — there's no license fee, and every part of the system, down to the desktop environment and window manager, can be swapped or configured.
- **The default environment for many developer tools** — Docker, most CI/CD pipelines, and many programming language ecosystems assume a Linux-like environment, even when developed on Windows or macOS.
- **Deep control over your system** — Linux exposes configuration and internals that other operating systems hide, which is valuable for learning how computers actually work.
- **Easy to try without commitment** — live USBs, virtual machines, and WSL let you experiment with Linux without replacing your existing operating system.

Download: [ubuntu.com/download](https://ubuntu.com/download) (a common first distribution — see Lesson 1 for how to choose one)

## Table of Contents

**Getting Started**
   1. **[What Is Linux](./[1]-What-Is-Linux.md)**  
       1.1 The Linux Kernel vs A Distribution  
       1.2 Open Source Philosophy  
       1.3 Common Distributions (Ubuntu, Fedora, Debian, Arch)  
       1.4 Choosing A Distribution  
   2. **[Installing And Trying Linux](./[2]-Installing-And-Trying-Linux.md)**  
       2.1 Live USBs And Virtual Machines  
       2.2 Dual-Booting vs A Full Install  
       2.3 The Installation Process  
       2.4 WSL (Windows Subsystem For Linux) As An Alternative  

**The Command Line**
   3. **[The Linux File System](./[3]-The-Linux-File-System.md)**  
       3.1 The Directory Structure (/, /home, /etc, /var...)  
       3.2 File Permissions And Ownership  
       3.3 Hidden Files And Configuration  
       3.4 Mounting Drives And Filesystems  
   4. **[The Command Line And Shell](./[4]-The-Command-Line-And-Shell.md)**  
       4.1 The Terminal And Common Shells (Bash, Zsh)  
       4.2 Essential Commands (ls, cd, cp, mv, grep...)  
       4.3 Piping And Redirection  
       4.4 Shell Script Basics  

**System Administration**
   5. **[Package Management](./[5]-Package-Management.md)**  
       5.1 Package Managers (apt, dnf, pacman)  
       5.2 Installing, Updating, And Removing Software  
       5.3 Repositories And PPAs  
       5.4 Flatpak, Snap, And AppImage  
   6. **[Users, Permissions, And Processes](./[6]-Users,-Permissions,-And-Processes.md)**  
       6.1 Users And Groups  
       6.2 sudo And Root Privileges  
       6.3 Viewing And Managing Processes  
       6.4 systemd And Services  

**Using Linux Day To Day**
   7. **[Using Linux Day To Day](./[7]-Using-Linux-Day-To-Day.md)**  
       7.1 Desktop Environments (GNOME, KDE, XFCE)  
       7.2 Common Applications And Alternatives To Windows/Mac Software  
       7.3 Linux For Development (Why Many Developers Prefer It)  
       7.4 Getting Help (man Pages, Community, Documentation)  
