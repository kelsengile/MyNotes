[Previous](./[31]-Backup-and-Cleanup-Scripts.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[33]-Packaging-and-Distributing-Scripts.md)

*Integration & Tooling*

# Lesson 32 - Version Control For Scripts

## 32.1 Why Version-Control Scripts

Scripts change over time, get shared with teammates, and sometimes need to be rolled back. Git provides history, collaboration, and safety for scripts just as it does for application code.

---

## 32.2 Basic Git Workflow

```bash
git init
git add script.sh
git commit -m "Add backup script"

git status
git log --oneline
git diff
```

---

## 32.3 Branching for Changes

```bash
git checkout -b add-logging
# ... make changes ...
git add .
git commit -m "Add logging to backup script"
git checkout main
git merge add-logging
```

---

## 32.4 Ignoring Generated and Sensitive Files

Create a `.gitignore` to exclude logs, secrets, and generated files from version control:

```
*.log
.env
__pycache__/
venv/
backups/
```

Never commit files containing credentials or API keys — use environment variables or `.env` files (Lesson 15) instead, and double check `.gitignore` is in place before the first commit.

---

[Previous](./[31]-Backup-and-Cleanup-Scripts.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[33]-Packaging-and-Distributing-Scripts.md)
