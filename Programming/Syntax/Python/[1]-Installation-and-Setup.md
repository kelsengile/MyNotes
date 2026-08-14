[Previous](./[0]-Introduction-to-Python.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[2]-Running-Python-Code.md)

# Lesson 1 - Installing Python & First-Time Setup

---

## 1.1 Why Install Python Locally

Even though Python is available in many online sandboxes, installing it locally gives you a real development environment: a persistent interpreter, access to the file system, the ability to install third-party packages, and the tools you'll use for every later lesson in this course. Everything from here on assumes you have Python running on your own machine.

---

## 1.2 Installing on Windows, macOS, and Linux


**Windows**
1. Download the installer from [python.org/downloads](https://www.python.org/downloads/).
2. Run the installer and **check "Add python.exe to PATH"** before clicking Install — this is the most commonly missed step.
3. Choose "Install Now" for the default setup.

**macOS**
- macOS does not ship with a version of Python meant for development. Install the latest version from [python.org](https://www.python.org/downloads/) or via [Homebrew](https://brew.sh):
  ```bash
  brew install python
  ```

**Linux**
- Most distributions include Python 3, but it may be an older version. On Debian/Ubuntu-based systems:
  ```bash
  sudo apt update
  sudo apt install python3 python3-pip
  ```
- On Fedora:
  ```bash
  sudo dnf install python3 python3-pip
  ```

---

## 1.3 Verifying the Installation



Open a terminal (Command Prompt, Terminal, or your shell of choice) and run:

```bash
python3 --version
```

On Windows, this is sometimes just `python --version`. You should see output like `Python 3.12.4`. If you get a "command not found" error, the installer likely didn't add Python to your system PATH — reinstall and make sure that option is checked, or add it manually.

Also check that `pip`, Python's package installer, is available:

```bash
pip3 --version
```

---

## 1.4 Choosing an Editor or IDE


You can write Python in any plain text editor, but a code-aware editor makes learning much easier. Common beginner-friendly choices:

- **VS Code** — free, lightweight, excellent Python extension with debugging and linting built in.
- **PyCharm (Community Edition)** — a full-featured Python-specific IDE, free and beginner-friendly.
- **Thonny** — designed specifically for teaching, with a simple built-in debugger.

Any of these will work for the rest of this course. Pick one, install it, and confirm you can create and save a `.py` file before moving on.

---

[Previous](./[0]-Introduction-to-Python.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[2]-Running-Python-Code.md)
