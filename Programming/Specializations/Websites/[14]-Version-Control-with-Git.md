[Previous](./[13]-Module-Bundlers.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[15]-Linting-and-Formatting.md)

*Tooling & Build Systems*

# Lesson 14 - Version Control with Git & GitHub for Web Projects

## 14.1 What Git Is

**Git** is a distributed version control system that tracks changes to files over time. Every saved snapshot is a **commit**, forming a history you can review, compare, or revert to. Unlike simply keeping copies of a folder, Git tracks precisely what changed, when, and by whom, and lets multiple people work on the same project without overwriting each other's work.

---

## 14.2 The Core Workflow

```bash
git init                       # start tracking a project
git add index.html style.css   # stage specific changes
git commit -m "Add homepage"   # save a snapshot
git status                     # see what's changed
git log                        # view commit history
```

Staging (`git add`) lets you choose exactly which changes go into the next commit, rather than committing every modified file at once.

---

## 14.3 Branches

A **branch** is an independent line of development. The default branch (commonly `main`) usually represents the stable, deployable version of the project; new work happens on separate branches so it doesn't disturb `main` until it's ready:

```bash
git branch feature/navbar
git checkout feature/navbar
# or: git checkout -b feature/navbar (create + switch)
```

---

## 14.4 GitHub and Remotes

**GitHub** hosts Git repositories online, enabling collaboration, backups, and code review. A **remote** is a version of the repository hosted elsewhere:

```bash
git remote add origin https://github.com/user/repo.git
git push origin main     # upload local commits
git pull origin main     # download and merge remote commits
git clone <url>          # copy an existing repository locally
```

---

## 14.5 Pull Requests and Merging

A **pull request (PR)** proposes merging one branch into another, giving collaborators a place to review the diff, leave comments, and request changes before it's merged. Once approved, merging combines the branch's history into the target branch. This project's own `CONTRIBUTING.md` describes exactly this workflow — fork, branch, commit, and open a pull request — which is the standard pattern across nearly all open-source projects on GitHub.

---

## 14.6 .gitignore

Not everything belongs in version control — generated files (`node_modules/`, build output, `.env` secrets) should be excluded via a `.gitignore` file:

```
node_modules/
dist/
.env
```

Committing these bloats the repository and, in the case of `.env`, can leak secrets (Lesson 27).

---

[Previous](./[13]-Module-Bundlers.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[15]-Linting-and-Formatting.md)
