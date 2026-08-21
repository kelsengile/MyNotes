[Previous](./[2]-Choosing-a-Scripting-Language.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[4]-Introduction-to-the-Command-Line.md)

*Getting Started*

# Lesson 3 - Setting Up Your Scripting Environment

## 3.1 Setting Up Bash

Bash is preinstalled on Linux and macOS. On Windows, use **WSL (Windows Subsystem for Linux)** or **Git Bash** to get a Bash environment. Verify your version with:

```bash
bash --version
```

A good text editor (VS Code, Vim, or Nano) and a terminal emulator are the only other tools you need to start.

---

## 3.2 Setting Up Python

Install Python from [python.org](https://python.org) or your system's package manager. Verify with:

```bash
python3 --version
```

It's best practice to use a **virtual environment** per project so dependencies don't clash:

```bash
python3 -m venv venv
source venv/bin/activate   # Linux/macOS
venv\Scripts\activate      # Windows
pip install <package>
```

---

## 3.3 Setting Up PowerShell

Windows ships with Windows PowerShell by default. For the modern, cross-platform version (PowerShell 7+), install it from Microsoft's official releases. Verify with:

```powershell
$PSVersionTable.PSVersion
```

---

## 3.4 Editors and Tooling

Recommended tooling for all three languages:
- **VS Code** with extensions: Bash IDE, Python, and PowerShell
- **ShellCheck** — lints Bash scripts for common mistakes
- **Git** — for version control (covered later in this course)

Make sure your editor is configured to save files with Unix line endings (`LF`) for Bash scripts, since Windows-style line endings (`CRLF`) can break shebang lines and cause cryptic errors.

---

[Previous](./[2]-Choosing-a-Scripting-Language.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[4]-Introduction-to-the-Command-Line.md)
