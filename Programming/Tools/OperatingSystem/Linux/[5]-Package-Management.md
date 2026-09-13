[Previous](./[4]-The-Command-Line-And-Shell.md) | [Table of Contents](./[0]-Introduction-to-Linux.md) | [Next](./[6]-Users,-Permissions,-And-Processes.md)

*System Administration*

# Lesson 5 - Package Management

## 5.1 Package Managers (apt, dnf, pacman)

A **package manager** installs, updates, and removes software along with its dependencies automatically, replacing the manual "download an installer and run it" process common on Windows and macOS.

```
You run:  install "nginx"
                │
                ▼
     Package Manager resolves:
       nginx
        ├── depends on: openssl
        ├── depends on: pcre
        └── depends on: zlib
                │
                ▼
     Downloads and installs all four, in the right order
```

| Distribution Family | Package Manager | Package Format |
|---|---|---|
| Debian/Ubuntu | `apt` (front-end for `dpkg`) | `.deb` |
| Fedora/RHEL-based | `dnf` (successor to `yum`) | `.rpm` |
| Arch-based | `pacman` | `.pkg.tar.zst` |

Each package manager reads from a list of configured **repositories** — remote servers hosting pre-built packages — so installing software rarely requires manually downloading files from a website at all.

---

## 5.2 Installing, Updating, And Removing Software

The core operations look conceptually identical across package managers, even though the exact command differs:

| Task | apt (Ubuntu/Debian) | dnf (Fedora) | pacman (Arch) |
|---|---|---|---|
| Update package list | `sudo apt update` | `sudo dnf check-update` | `sudo pacman -Sy` |
| Install a package | `sudo apt install nginx` | `sudo dnf install nginx` | `sudo pacman -S nginx` |
| Upgrade all packages | `sudo apt upgrade` | `sudo dnf upgrade` | `sudo pacman -Syu` |
| Remove a package | `sudo apt remove nginx` | `sudo dnf remove nginx` | `sudo pacman -R nginx` |
| Search for a package | `apt search nginx` | `dnf search nginx` | `pacman -Ss nginx` |

```
$ sudo apt update && sudo apt upgrade
Reading package lists... Done
23 packages can be upgraded.
```

**Updating the package list** (`apt update`) only refreshes the *catalog* of what's available and at what version — it doesn't install anything by itself. The actual upgrade step (`apt upgrade`) is what downloads and installs newer versions of already-installed packages.

---

## 5.3 Repositories And PPAs

**Repositories** are the servers a package manager checks when resolving package names to actual files, configured in files like `/etc/apt/sources.list` (Debian/Ubuntu) or `/etc/yum.repos.d/` (Fedora).

```
/etc/apt/sources.list
deb http://archive.ubuntu.com/ubuntu jammy main restricted
deb http://archive.ubuntu.com/ubuntu jammy-updates main restricted
```

- **Official repositories** — maintained and vetted by the distribution itself, offering the highest level of trust and stability.
- **PPAs (Personal Package Archives)** — Ubuntu-specific third-party repositories, often maintained by a software's own developers, offering newer versions than the official repos carry (e.g. `sudo add-apt-repository ppa:some/ppa`).
- **Trust considerations** — adding a third-party repository means trusting that maintainer's packages run with system-level privileges during install; only add repositories from sources you trust, since a malicious package can affect the whole system.

---

## 5.4 Flatpak, Snap, And AppImage

Beyond a distribution's native package manager, several **universal package formats** aim to work identically across any Linux distribution, bundling most or all of an app's dependencies inside the package itself.

```
Traditional package (.deb)        Universal package (Flatpak/Snap)
 ┌─────────────┐                 ┌─────────────────────────┐
 │ App only        │                 │ App + its own dependencies │
 │ (relies on system│                 │ (isolated from the rest of  │
 │  libraries)       │                 │  the system)                 │
 └─────────────┘                 └─────────────────────────┘
```

| Format | Backed By | Sandbox? | Typical Use |
|---|---|---|---|
| Flatpak | Community/Red Hat | Yes | Desktop apps (via Flathub) |
| Snap | Canonical (Ubuntu) | Yes | Desktop apps and server software |
| AppImage | Community | No (runs directly) | Single portable executable, no install step |

- **Flatpak** and **Snap** both run apps in a sandbox with restricted access to the rest of the system by default, improving security at the cost of a somewhat larger download size (since shared libraries are duplicated per app rather than shared system-wide).
- **AppImage** requires no installation at all — download the single file, mark it executable (`chmod +x`, see Lesson 3.2), and run it directly, useful for trying software without touching the system's package database.
- **Why these exist** — they let a developer publish one package that works identically on Ubuntu, Fedora, Arch, and beyond, sidestepping the need to build and maintain separate `.deb`, `.rpm`, and `pacman` packages for the same piece of software.

[Previous](./[4]-The-Command-Line-And-Shell.md) | [Table of Contents](./[0]-Introduction-to-Linux.md) | [Next](./[6]-Users,-Permissions,-And-Processes.md)
