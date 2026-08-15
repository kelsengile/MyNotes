[Previous](./[2]-Running-Python-Code.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[4]-Environment-Variables-and-Configuration.md)

*Getting Started*

# Lesson 3 - Virtual Environments & Package Management

## 3.1 Why Virtual Environments Matter

A **virtual environment** is an isolated, self-contained copy of Python and its installed packages, separate from your system-wide Python install. Without one, every project on your computer shares the same set of installed packages — so if Project A needs `django==3.2` and Project B needs `django==5.0`, you're stuck. Virtual environments solve this by giving each project its own independent set of dependencies.

---

## 3.2 venv - Python's Built-in Tool

`venv` ships with Python itself, so no extra installation is needed. Create one inside your project folder:

```bash
python -m venv .venv
```

This creates a `.venv` folder containing an isolated Python interpreter and package directory. Activate it:

```bash
# Windows
.venv\Scripts\activate

# macOS / Linux
source .venv/bin/activate
```

Once activated, your terminal prompt usually shows `(.venv)`, and any packages you install with `pip` go into this isolated environment instead of your system Python. Deactivate with:

```bash
deactivate
```

---

## 3.3 pip - The Package Installer

`pip` is Python's standard package manager, used to install libraries from the [Python Package Index (PyPI)](https://pypi.org).

```bash
pip install requests          # install a package
pip install requests==2.31.0  # install a specific version
pip uninstall requests        # remove a package
pip list                      # list installed packages
pip show requests             # show details about a package
```

---

## 3.4 requirements.txt

To make a project's dependencies reproducible on another machine, save them to a `requirements.txt` file:

```bash
pip freeze > requirements.txt
```

Anyone else can then recreate the exact same environment with:

```bash
pip install -r requirements.txt
```

---

## 3.5 Poetry

[Poetry](https://python-poetry.org) is a modern, higher-level dependency and packaging manager that combines virtual environment creation, dependency resolution, and package publishing in one tool, configured through a single `pyproject.toml` file instead of `requirements.txt`.

```bash
poetry init          # start a new project
poetry add requests  # add a dependency
poetry install        # install all dependencies
poetry shell          # activate the virtual environment
```

Poetry automatically locks exact dependency versions (`poetry.lock`), which makes builds more reproducible than a loose `requirements.txt`.

---

## 3.6 Conda

[Conda](https://docs.conda.io) is a package and environment manager popular in the data science and scientific computing world. Unlike `pip`/`venv`, Conda can manage non-Python dependencies too (like C libraries), which matters for packages such as NumPy or TensorFlow.

```bash
conda create --name myenv python=3.12
conda activate myenv
conda install numpy pandas
```

For general-purpose Python development, `venv` + `pip` (or Poetry) is the more common choice; Conda tends to be preferred specifically for data science workflows.

[Previous](./[2]-Running-Python-Code.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[4]-Environment-Variables-and-Configuration.md)
