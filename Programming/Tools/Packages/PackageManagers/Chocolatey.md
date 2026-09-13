[⬅ Back to Packages Fundamentals](../[0]-Introduction-to-Packages.md)

# Chocolatey

Chocolatey is a popular third-party package manager for Windows. It wraps installers (`.exe`, `.msi`) and scripts into a single package format, so software can be installed and updated from the command line like on Linux or macOS.

Download [https://chocolatey.org/install](https://chocolatey.org/install)

---

## What Is Chocolatey?

Windows historically lacked a built-in package manager, so most software was installed by downloading and running installers manually. Chocolatey fills that gap with a community-maintained package repository and a consistent command-line interface.

---

## Core Commands

```powershell
choco install <package>          # Install a package
choco install <package> -y       # Install without confirmation prompts
choco upgrade <package>          # Upgrade a specific package
choco upgrade all -y             # Upgrade every installed package
choco uninstall <package>        # Remove a package
choco search <keyword>           # Search for a package
choco list --local-only          # List packages installed on this machine
```

---

## Chocolatey vs. Winget

Winget is Microsoft's official package manager and comes built into Windows 10/11. Chocolatey predates it and has a larger, community-driven package library — it's often used for software Winget doesn't carry yet, or for more advanced install scripting.

---

## Example Walkthrough

```powershell
choco install googlechrome -y
choco install 7zip -y
choco upgrade all -y
```

Installs Google Chrome and 7-Zip without prompts, then upgrades everything already on the machine.

[⬅ Back to Packages Fundamentals](../[0]-Introduction-to-Packages.md)
