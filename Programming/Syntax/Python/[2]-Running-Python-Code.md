[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[3]-Virtual-Environments-and-Pip.md)

*Getting Started*

# Lesson 2 - Running Code: Scripts, the REPL & Notebooks

## 2.1 The Python Interpreter

Python is an **interpreted language**: rather than compiling your code into a standalone executable ahead of time, the Python interpreter reads and executes your code line by line each time you run it. This is what makes Python fast to iterate with, at some cost to raw execution speed compared to compiled languages like C.

There are three common ways to run Python code, covered below: scripts, the REPL, and notebooks.

---

## 2.2 Running Scripts (.py files)

The most common way to run Python is to save code in a file ending in `.py` and execute the whole file at once. Create a file named `hello.py`:

```python
print("Hello, World!")
```

Then run it from a terminal in the same folder:

```bash
python hello.py
```

Scripts are the standard way to write and share real, reusable Python programs.

---

## 2.3 The Interactive REPL

REPL stands for **Read-Eval-Print Loop**. Typing `python` (with no filename) at the terminal drops you into an interactive session where you type one line of code at a time and see the result immediately:

```bash
$ python
>>> 2 + 2
4
>>> print("hi")
hi
>>> exit()
```

The REPL is great for quickly testing small snippets of code or exploring how a function behaves, but it isn't meant for writing full programs — nothing you type is saved to a file.

---

## 2.4 Jupyter Notebooks

Jupyter notebooks (`.ipynb` files) mix code, output, and formatted text (including images and charts) in a single document, organized into runnable "cells." They're especially popular in data science and machine learning because you can run one cell at a time and immediately see plots, tables, and results inline.

Install and launch Jupyter with:

```bash
pip install notebook
jupyter notebook
```

This opens a browser tab where you can create and run notebooks.

---

## 2.5 Which Should You Use?

- **Scripts** — for real programs, applications, and anything you intend to save, share, or run repeatedly.
- **The REPL** — for quick, throwaway experiments and checking how a single line of code behaves.
- **Notebooks** — for data exploration, visualization, and step-by-step analysis where seeing output alongside code matters.

This course primarily uses scripts, with the REPL used for short demonstrations.

[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[3]-Virtual-Environments-and-Pip.md)
