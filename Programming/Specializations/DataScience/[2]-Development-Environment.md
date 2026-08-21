*Getting Started*

# Lesson 2 - Setting Up Your Environment (Python, Jupyter, Conda)

[Previous](./[1]-What-is-Data-Science.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[3]-Anatomy-of-a-Data-Science-Project.md)

---

## 2.1 Why Python?

Python is the dominant language for data science because of its readable syntax and its enormous ecosystem of libraries (NumPy, Pandas, scikit-learn, TensorFlow, PyTorch, and more — all covered later in this course). Throughout this course, code examples use Python 3.

---

## 2.2 Installing Python with Conda

**Conda** is a package and environment manager that makes it easy to install Python along with data science libraries without version conflicts.

1. Install **Miniconda** (a lightweight Conda installer) from the official Anaconda website.
2. Create a new environment dedicated to this course:
   ```bash
   conda create -n datascience python=3.11
   ```
3. Activate the environment:
   ```bash
   conda activate datascience
   ```
4. Install the core libraries used throughout this course:
   ```bash
   conda install numpy pandas matplotlib seaborn scikit-learn jupyter
   ```

Using a dedicated environment per project avoids one project's library versions breaking another's.

---

## 2.3 Jupyter Notebooks

**Jupyter Notebook** (and its newer interface, **JupyterLab**) lets you write and run Python code in cells, mixing code, output, charts, and notes (Markdown) in a single document. This makes it the standard tool for exploratory data science work.

To launch it:
```bash
jupyter notebook
```
This opens a browser tab where you can create a new `.ipynb` notebook file. Key habits:

- Run cells top to bottom to avoid confusing hidden state.
- Use Markdown cells to document your reasoning, not just code cells.
- Restart the kernel and "Run All" occasionally to confirm your notebook still works from a clean state.

---

## 2.4 Alternative Tools

- **VS Code** — a general-purpose code editor with strong Python and Jupyter notebook support, popular for larger projects.
- **Google Colab** — a free, cloud-hosted Jupyter environment with no local installation required, useful for quick experiments or when you need free GPU access.
- **pip** — Python's built-in package installer (`pip install <package>`), an alternative to Conda for managing libraries, often used inside virtual environments (`venv`).

Any of these will work for the rest of this course — pick whichever fits your machine and workflow.

---

[Previous](./[1]-What-is-Data-Science.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[3]-Anatomy-of-a-Data-Science-Project.md)
