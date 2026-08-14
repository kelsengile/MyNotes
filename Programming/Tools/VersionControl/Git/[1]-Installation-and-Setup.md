[Previous](./[0]-Introduction-to-Git.md) | [Table of Contents](./[0]-Introduction-to-Git.md) | [Next](./[2]-Git-Basics.md)

# Lesson 1 - Installing Git And First-Time Setup

## 1.1 What is Git?

Git is a distributed version control system that tracks changes to files over time, letting you and others collaborate on a project without overwriting each other's work.

---

## 1.2 Installing Git

### Windows
Download the installer from [git-scm.com](https://git-scm.com/) and run it. This also installs **Git Bash**, a terminal for running Git commands.

### macOS
```bash
brew install git
```
Or install via Xcode Command Line Tools:
```bash
xcode-select --install
```

### Linux (Debian/Ubuntu)
```bash
sudo apt update
sudo apt install git
```

### Linux (Fedora)
```bash
sudo dnf install git
```

---

## 1.3 Verifying Installation

```bash
git --version
```

---

## 1.4 First-Time Setup

Before making any commits, tell Git who you are. This information is attached to every commit you make.

```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

### Useful Initial Config Options

Set your default branch name (modern convention is `main`):
```bash
git config --global init.defaultBranch main
```

Set your default text editor (used for commit messages, rebases, etc.):
```bash
git config --global core.editor "code --wait"   # VS Code
git config --global core.editor "vim"           # Vim
git config --global core.editor "nano"          # Nano
```

Enable helpful colored output:
```bash
git config --global color.ui auto
```

### Checking Your Configuration

```bash
git config --list
git config user.name
git config user.email
```

Configuration is stored in three possible levels, from lowest to highest priority:

| Level | Flag | File Location |
|---|---|---|
| System | `--system` | `/etc/gitconfig` |
| Global | `--global` | `~/.gitconfig` |
| Local (per-repo) | `--local` (default) | `.git/config` |

---

## 1.5 SSH Keys (Optional but Recommended)

For pushing to remotes like GitHub/GitLab without typing a password every time:

```bash
ssh-keygen -t ed25519 -C "you@example.com"
```

Then add the public key (`~/.ssh/id_ed25519.pub`) to your GitHub/GitLab account under SSH keys.

Test the connection:
```bash
ssh -T git@github.com
```

---

## 1.6 Summary

- Install Git via your OS package manager or the official installer.
- Set `user.name` and `user.email` — Git won't let you commit meaningfully without them.
- Optionally configure your default editor, default branch name, and SSH keys.

---

[Previous](./[0]-Introduction-to-Git.md) | [Table of Contents](./[0]-Introduction-to-Git.md) | [Next](./[2]-Git-Basics.md)
