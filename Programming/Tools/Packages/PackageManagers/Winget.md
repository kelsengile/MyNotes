[⬅ Back to Packages Fundamentals](../[0]-Introduction-to-Packages.md)

# Winget

Winget (Windows Package Manager) is Microsoft's official command-line package manager, built into Windows 10 and 11. It installs software directly from Microsoft's curated repository, without needing to install anything extra first.

Download [https://learn.microsoft.com/windows/package-manager/winget/](https://learn.microsoft.com/windows/package-manager/winget/)

---

## What Is Winget?

Winget was Microsoft's answer to the lack of a built-in package manager on Windows. It pulls from the official Windows Package Manager Community Repository, which includes both Microsoft and third-party software.

---

## Core Commands

```powershell
winget install <package>         # Install a package
winget uninstall <package>       # Remove a package
winget upgrade                   # List packages with available updates
winget upgrade --all             # Upgrade every installed package
winget search <keyword>          # Search for a package
winget list                      # List installed packages
```

---

## Finding the Right Package ID

Search returns an exact package ID, which is more reliable to install with than a plain name:

```powershell
winget search git
# Name    Id         Version
# Git     Git.Git    2.45.2

winget install Git.Git
```

---

## Winget vs. Chocolatey

Winget is built into Windows and backed officially by Microsoft, making it the simplest default choice. Chocolatey has existed longer and has a larger community package library, which is why it's still commonly used alongside or instead of Winget.

---

## Example Walkthrough

```powershell
winget search "visual studio code"
winget install Microsoft.VisualStudioCode
winget upgrade --all
```

Searches for VS Code, installs it by its exact package ID, then upgrades everything else already installed.

[⬅ Back to Packages Fundamentals](../[0]-Introduction-to-Packages.md)
