[Previous](./[2]-Running-Python-Code.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[4]-Variables-and-Data-Types.md)

# Lesson 3 - Virtual Environments & Package Management

---

## 3.1 Why Virtual Environments Matter



Every Python project can depend on different, sometimes conflicting versions of the same third-party package. A virtual environment is an isolated, self-contained copy of Python plus its own set of installed packages, so one project's dependencies never interfere with another's. Creating a virtual environment per project is considered standard practice.

---

## 3.2 venv Basics



`venv` is built into Python's standard library — no extra installation needed.

```bash
# Create an environment named "venv" in the current folder
python3 -m venv venv

# Activate it
source venv/bin/activate      # macOS/Linux
venv\Scripts\activate         # Windows

# Deactivate when you're done
deactivate
```

While activated, your terminal prompt usually shows the environment name, and any `pip install` only affects that environment.

---

## 3.3 pip Basics


`pip` is Python's default package installer, pulling packages from the [Python Package Index (PyPI)](https://pypi.org).

```bash
pip install requests            # install a package
pip install requests==2.31.0    # install a specific version
pip uninstall requests          # remove a package
pip list                        # list installed packages
pip freeze > requirements.txt   # save exact versions to a file
pip install -r requirements.txt # install everything from that file
```

`requirements.txt` is the conventional way to share which packages (and versions) a project needs.

---

## 3.4 poetry and conda Overview


`venv` + `pip` cover most needs, but two alternatives are common in larger projects:

- **Poetry** — manages dependencies, virtual environments, and packaging together, using a `pyproject.toml` file instead of `requirements.txt`. Popular for application and library development.
  ```bash
  poetry init
  poetry add requests
  poetry install
  ```
- **conda** — a package *and* environment manager from the Anaconda ecosystem, widely used in data science because it can also install non-Python dependencies (like compiled scientific libraries).
  ```bash
  conda create -n myenv python=3.12
  conda activate myenv
  conda install numpy
  ```

For general-purpose Python development, `venv` and `pip` are enough to get started; reach for Poetry or conda as your projects grow more complex.

---

[Previous](./[2]-Running-Python-Code.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[4]-Variables-and-Data-Types.md)
