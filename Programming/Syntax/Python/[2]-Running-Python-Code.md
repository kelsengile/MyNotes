[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[3]-Virtual-Environments-and-Pip.md)

# Lesson 2 - Running Python Code: Scripts, the REPL & Notebooks

---

## 2.1 The Interactive REPL



REPL stands for Read-Eval-Print Loop. Typing `python3` (or `python` on Windows) in your terminal with no file argument drops you into it:

```bash
$ python3
>>> 2 + 2
4
>>> print("hello")
hello
```

Each line is read, evaluated, and its result printed immediately. The REPL is ideal for quick experiments — testing a function, checking how an operator behaves — but code typed here is not saved anywhere. Exit with `exit()` or `Ctrl+D` (`Ctrl+Z` then Enter on Windows).

---

## 2.2 Running Script Files



For anything you want to save and reuse, write a `.py` file and run it as a script:

```bash
python3 my_script.py
```

Python executes the file from top to bottom. This is how almost all real Python programs are run, and it's the format used for exercise files throughout this course.

---

## 2.3 Jupyter Notebooks



A Jupyter Notebook (`.ipynb`) mixes code, output, and formatted text (Markdown) in a single document, split into runnable "cells." It's popular in data science because you can run one cell at a time and immediately see results — tables, plots, etc. — without rerunning the whole file.

Install and launch it with:

```bash
pip install notebook
jupyter notebook
```

This opens a browser interface where you create and run notebooks.

---

## 2.4 Choosing the Right Mode


| Mode | Best for |
|---|---|
| REPL | Quick one-off checks, exploring a new function |
| Script (`.py`) | Reusable programs, applications, automation |
| Notebook (`.ipynb`) | Data exploration, visualization, step-by-step analysis |

For this course, most lessons and exercises will use plain `.py` script files, since that's the format you'll use most in real-world Python development.

---

[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-Python.md) | [Next](./[3]-Virtual-Environments-and-Pip.md)
