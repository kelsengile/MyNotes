[⬅ Back to Package Managers](../[0]-Introduction-to-Packages.md)

# pip

pip is the standard package manager for Python. It installs libraries from the Python Package Index (PyPI) directly into your Python environment, and ships with Python itself.

Download [https://pip.pypa.io/en/stable/installation/](https://pip.pypa.io/en/stable/installation/)

---

## What Is pip?

pip installs packages from [pypi.org](https://pypi.org), the official public registry for Python libraries. Unlike npm or Cargo, pip doesn't require a project configuration file by default — though it's standard practice to track dependencies in a `requirements.txt` file.

---

## Core Commands

```bash
pip install <package>              # Install a package
pip install <package>==1.2.0       # Install a specific version
pip uninstall <package>            # Remove a package
pip list                           # List installed packages
pip show <package>                 # Show details about an installed package
pip freeze > requirements.txt      # Save exact installed versions to a file
pip install -r requirements.txt    # Install everything listed in that file
```

---

## Virtual Environments

Installing packages globally means every project shares the same versions — which quickly causes conflicts. Python's `venv` module creates an isolated environment per project, and pip installs into whichever environment is currently active.

```bash
python -m venv myenv           # Create a virtual environment
source myenv/bin/activate      # Activate it (Linux/macOS)
myenv\Scripts\activate         # Activate it (Windows)
pip install requests           # Installs only into this environment
```

---

## Example Walkthrough

```bash
python -m venv venv
source venv/bin/activate
pip install requests
pip freeze > requirements.txt
```

Creates and activates a virtual environment, installs the `requests` library into it, then records the exact installed versions.

[⬅ Back to Package Managers](../[0]-Introduction-to-Packages.md)
