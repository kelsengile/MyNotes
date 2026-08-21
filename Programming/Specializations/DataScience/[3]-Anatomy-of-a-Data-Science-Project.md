*Getting Started*

# Lesson 3 - Anatomy of a Data Science Project

[Previous](./[2]-Development-Environment.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[4]-Python-Fundamentals-for-Data-Science.md)

---

## 3.1 Project Folder Structure

A typical data science project keeps things organized so that data, code, and results don't get tangled together:

```
project/
├── data/
│   ├── raw/          # original, untouched data
│   └── processed/    # cleaned/transformed data
├── notebooks/         # exploratory Jupyter notebooks
├── src/                # reusable Python scripts/modules
├── models/             # saved trained models
├── reports/            # figures, summaries, final write-ups
└── README.md
```

A golden rule: never overwrite raw data. Always write cleaned versions to a separate location so the original is recoverable.

---

## 3.2 The CRISP-DM Framework

**CRISP-DM** (Cross-Industry Standard Process for Data Mining) is a widely used framework describing the phases of a data science project:

1. **Business Understanding** — what problem are we solving, and what does success look like?
2. **Data Understanding** — what data is available, and what does it actually contain?
3. **Data Preparation** — cleaning and transforming data into a usable form.
4. **Modeling** — applying statistical or machine learning techniques.
5. **Evaluation** — checking whether the model actually solves the business problem.
6. **Deployment** — putting the model into production use.

These phases aren't strictly linear — teams frequently revisit earlier phases as they learn more.

---

## 3.3 Version Control Basics

**Git** is the standard tool for tracking changes to code (and often notebooks). Core commands you'll use constantly:

```bash
git init                 # start tracking a project
git add file.py           # stage a change
git commit -m "message"   # save a snapshot
git push                  # upload to a remote repo (e.g. GitHub)
```

For data science specifically, it's common to track code with Git but keep large datasets out of Git (using `.gitignore`), relying instead on tools like DVC or cloud storage for data versioning.

---

## 3.4 Documentation Habits

Good projects include a `README.md` describing what the project does, how to set it up, and how to reproduce results. Clear documentation and reproducibility (covered further in Lesson 46) are what separate a one-off notebook from a project other people (including future-you) can trust and reuse.

---

[Previous](./[2]-Development-Environment.md) | [Table of Contents](./[0]-Introduction-to-DataScience.md) | [Next](./[4]-Python-Fundamentals-for-Data-Science.md)
