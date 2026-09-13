[⬅ Back to Packages Fundamentals](../[0]-Introduction-to-Packages.md)

# Homebrew

Homebrew ("brew") is a package manager for macOS and Linux. It installs software into its own directory rather than system folders, which keeps things tidy and means most installs don't need `sudo`.

Download [https://brew.sh/](https://brew.sh/)

---

## What Is Homebrew?

macOS doesn't ship with a command-line package manager, so Homebrew fills that role — similar to how APT works on Debian/Ubuntu. Packages are called "formulae," and Homebrew also supports "casks" for installing full macOS applications (like Chrome or Slack).

---

## Core Commands

```bash
brew update                  # Update Homebrew itself and its package lists
brew upgrade                 # Upgrade all installed packages
brew install <package>       # Install a package (formula)
brew install --cask <app>    # Install a macOS application (cask)
brew uninstall <package>     # Remove a package
brew search <keyword>        # Search for a package
brew list                    # List installed packages
```

---

## Formulae vs. Casks

A **formula** is a command-line tool or library (like `wget` or `node`). A **cask** is a full graphical application (like `google-chrome` or `visual-studio-code`). Both install through the same `brew install` command, just with the `--cask` flag for the latter.

---

## Example Walkthrough

```bash
brew update
brew install wget
brew install --cask google-chrome
```

Updates Homebrew, installs the `wget` command-line tool, then installs Google Chrome as a full application.

[⬅ Back to Packages Fundamentals](../[0]-Introduction-to-Packages.md)
