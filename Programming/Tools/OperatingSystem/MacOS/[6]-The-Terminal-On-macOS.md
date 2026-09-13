[Previous](./[5]-Built-In-Apps-And-The-App-Ecosystem.md) | [Table of Contents](./[0]-Introduction-to-MacOS.md) | [Next](./[7]-Using-macOS-For-Development.md)

*System Features*

# Lesson 6 - The Terminal On macOS

## 6.1 macOS's Unix Foundation

Underneath its graphical interface, macOS is built on **Darwin**, an open-source Unix-based operating system core (technically certified as UNIX 03 compliant since Mac OS X Leopard). This is a big part of why macOS is so popular with developers: the same command-line tools, file permission model, and scripting behavior found on Linux servers largely apply on a Mac, too.

```
        macOS (what you see: Finder, Dock, apps)
                        │
                     Darwin
              (Unix core: processes, files,
               permissions, networking)
                        │
                 XNU Kernel (hybrid of
               Mach + BSD components)
```

In practice, this means a developer can write a shell script, a Python program, or configure a web server locally on a Mac and expect it to behave the same way it will once deployed to a Linux server — a major reason Macs are common in professional software development.

## 6.2 Using Terminal.app And zsh

**Terminal** is the built-in app that gives you a command-line interface into that Unix core. You can open it via Spotlight (⌘ + Space, then type "Terminal") or from **Applications → Utilities**.

Since macOS Catalina, the default shell (the program that interprets your typed commands) is **zsh** (Z shell), replacing the older `bash`. A few everyday commands:

```bash
pwd                     # print current directory
ls -la                  # list files, including hidden ones, with details
cd Documents            # move into the Documents folder
mkdir new-project       # create a new folder
touch notes.txt         # create an empty file
open .                  # open the current folder in Finder
cat notes.txt           # print a file's contents
rm notes.txt            # delete a file (careful — no Trash/undo!)
```

The `open` command is a distinctly macOS convenience — it bridges the command line and the graphical Finder, letting you open files, folders, or even URLs from Terminal using their default app.

## 6.3 Homebrew As A Package Manager

macOS doesn't ship with a built-in package manager the way many Linux distributions do (like `apt` or `dnf`). **Homebrew** has become the de facto standard for filling that gap, letting you install command-line tools and some apps with a single command.

```bash
# Install Homebrew itself (run once, from the Homebrew website's official script)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Then install tools with:
brew install git
brew install node
brew install wget

# Keep everything up to date:
brew update && brew upgrade
```

Homebrew organizes installable software into **formulae** (command-line tools and libraries) and **casks** (full GUI applications, installed with `brew install --cask`). It resolves dependencies automatically — installing one tool that depends on another will pull both in without extra steps.

Download: [brew.sh](https://brew.sh)

## 6.4 When Developers Need The Terminal

Even developers who spend most of their day in a graphical code editor still reach for the Terminal regularly, for tasks the graphical interface doesn't cover well:

- **Version control** — running `git` commands to track and share code changes.
- **Running local servers** — starting a local web server or database to test an app before deploying it.
- **Package management** — installing project dependencies (`npm install`, `pip install`, etc.).
- **Automation and scripting** — writing shell scripts to automate repetitive tasks.
- **Remote access** — connecting to remote servers over `ssh` to manage them directly.
- **Troubleshooting** — inspecting system logs, file permissions, or running processes when something misbehaves in ways the GUI doesn't fully expose.

Because the Terminal gives direct, unfiltered access to the system, it's also less forgiving than the GUI — commands like `rm` don't move files to the Trash, they delete them immediately. This directness is exactly why it's powerful, and exactly why it rewards careful use.

---

[Previous](./[5]-Built-In-Apps-And-The-App-Ecosystem.md) | [Table of Contents](./[0]-Introduction-to-MacOS.md) | [Next](./[7]-Using-macOS-For-Development.md)
