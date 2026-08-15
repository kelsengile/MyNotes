[Previous](./[0]-Introduction-to-Python.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[2]-Running-Python-Code.md)

*Getting Started*

# Lesson 1 - Installation and Setup

## 1.1 What You Need Before You Start

You don't need much: a computer running Windows, macOS, or Linux, an internet connection to download the installer, and a code editor (covered in 1.6). Python itself is free and open source, maintained by the Python Software Foundation.

Most modern operating systems ship with *some* version of Python pre-installed (especially macOS and Linux), but it's often an outdated version used internally by the OS. It's best practice to install your own up-to-date copy rather than relying on the system version.

---

## 1.2 Installing Python on Windows

1. Go to [python.org/downloads](https://www.python.org/downloads/) and download the latest stable release.
2. Run the installer. **Check the box that says "Add python.exe to PATH"** before clicking Install — this lets you run `python` from any terminal window.
3. Once finished, open Command Prompt or PowerShell and confirm the install (see 1.5).

Alternatively, Windows users can install Python from the Microsoft Store, though the python.org installer gives more control over versions.

---

## 1.3 Installing Python on macOS

macOS includes a system Python, but you should install your own version instead of using it:

1. Download the macOS installer from [python.org/downloads](https://www.python.org/downloads/) and run it, **or**
2. Install [Homebrew](https://brew.sh) (a package manager for macOS) and run:
   ```bash
   brew install python
   ```

Homebrew is the more common approach among professional developers because it makes upgrading Python later a single command.

---

## 1.4 Installing Python on Linux

Most Linux distributions come with Python 3 pre-installed. Check your version first (see 1.5). If you need a newer version, use your distribution's package manager:

```bash
# Debian / Ubuntu
sudo apt update
sudo apt install python3 python3-pip

# Fedora
sudo dnf install python3 python3-pip

# Arch
sudo pacman -S python python-pip
```

---

## 1.5 Verifying Your Installation

Open a terminal (Command Prompt/PowerShell on Windows, Terminal on macOS/Linux) and run:

```bash
python --version
```

On macOS/Linux, if `python` isn't recognized or points to an old Python 2 install, try:

```bash
python3 --version
```

You should see output like `Python 3.12.4`. Also verify that `pip`, Python's package installer, is available:

```bash
pip --version
# or
pip3 --version
```

---

## 1.6 Choosing a Code Editor / IDE

You can technically write Python in Notepad, but a proper editor makes life much easier through syntax highlighting, autocompletion, and debugging tools. Popular choices:

- **VS Code** — free, lightweight, extremely popular; install the official "Python" extension from Microsoft.
- **PyCharm** — a full-featured IDE built specifically for Python, with a free Community edition.
- **Jupyter Notebook / JupyterLab** — great for data science and experimentation (covered in the next lesson).

For this course, VS Code with the Python extension is a solid, free default.

[Previous](./[0]-Introduction-to-Python.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[2]-Running-Python-Code.md)
