[⬅ Back to Package Managers](../[0]-Introduction-to-Packages.md)

# APT (Advanced Package Tool)

APT is the default package manager for Debian, Ubuntu, and their derivatives. It manages `.deb` packages, resolves dependencies automatically, and talks to remote repositories so you don't have to track any of that by hand.

Download: [https://ubuntu.com/server/docs/package-management/](https://ubuntu.com/server/docs/package-management/)

---

## What Is APT?

Before APT, installing software on Debian-based systems meant manually downloading `.deb` files and hoping you'd also grabbed every library they depended on. APT sits on top of that process and handles the whole thing: fetching packages, resolving dependencies, and keeping a record of what's installed.

---

## Core Commands

```bash
sudo apt update              # Refresh the list of available packages
sudo apt upgrade             # Upgrade all installed packages
sudo apt install <package>   # Install a package
sudo apt remove <package>    # Remove a package (keeps config files)
sudo apt purge <package>     # Remove a package and its config files
sudo apt autoremove          # Remove packages that are no longer needed
apt search <keyword>         # Search for a package by name/description
apt show <package>           # Show details about a package
```

`apt update` doesn't install anything — it just refreshes APT's local list of what's available. You need to run it before `install` or `upgrade` if you want the latest versions.

---

## APT vs. dpkg

`dpkg` is the lower-level tool that actually unpacks and installs `.deb` files, but it has no idea how to fetch a package's dependencies. APT is built on top of `dpkg`: it handles dependency resolution, downloads, and repositories, then hands the actual install off to `dpkg`.

```
APT  --resolves dependencies & downloads-->  dpkg  --installs-->  .deb file
```

You'll typically only reach for `dpkg` directly when installing a `.deb` file you downloaded manually:

```bash
sudo dpkg -i package-name.deb
sudo apt install -f     # Fix any missing dependencies dpkg couldn't resolve
```

---

## Repositories and Sources

APT installs only from repositories listed in `/etc/apt/sources.list` (and files under `/etc/apt/sources.list.d/`). Adding a third-party repository (a PPA, for example) means trusting that source with root-level access to your system, so only add ones you trust.

```bash
sudo add-apt-repository ppa:example/ppa   # Add a trusted third-party repository
sudo apt update                           # Required after adding a new repository
```

---

## Example Walkthrough

```bash
sudo apt update
sudo apt install git
git --version
sudo apt autoremove
```

This refreshes the package list, installs Git, confirms it's ready to use, then cleans up any leftover dependencies that are no longer needed.

[⬅ Back to Package Managers](../[0]-Introduction-to-Packages.md)