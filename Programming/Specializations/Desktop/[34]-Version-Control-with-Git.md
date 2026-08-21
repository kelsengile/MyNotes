[Previous](./[33]-Dependency-Management.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[35]-Testing-Desktop-Applications.md)

*Architecture & Best Practices*

# Lesson 34 - Version Control with Git

## 34.1 Why Version Control

Git tracks every change to a project's source over time, letting you see history, revert mistakes, and work on features in isolation without disturbing a working codebase. It's essential for desktop projects with multiple contributors, multiple platforms, and long-lived release branches.

---

## 34.2 Core Workflow

```bash
git clone <repo-url>
git checkout -b feature/dark-mode
# make changes
git add .
git commit -m "Add dark mode toggle to settings"
git push origin feature/dark-mode
```

A **commit** is a saved snapshot with a message describing the change; a **branch** is an independent line of development; a **pull request** proposes merging one branch's changes into another, typically after review.

---

## 34.3 What to Exclude

Desktop projects generate large, machine-specific, or regenerable files that shouldn't be committed: build output (`bin/`, `obj/`, `dist/`), dependency folders (`node_modules/`), user-specific IDE settings, and signing certificates/secrets. A `.gitignore` file lists patterns Git should never track:

```
bin/
obj/
node_modules/
*.pfx
.vs/
```

---

## 34.4 Merge Conflicts

A conflict happens when two branches change the same lines differently and Git can't merge them automatically. Git marks the conflicting sections in the file (`<<<<<<<`, `=======`, `>>>>>>>`); resolving means manually choosing (or combining) the correct content, removing the markers, and committing the result. Small, frequent commits and short-lived branches reduce how often conflicts occur.

[Previous](./[33]-Dependency-Management.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[35]-Testing-Desktop-Applications.md)
