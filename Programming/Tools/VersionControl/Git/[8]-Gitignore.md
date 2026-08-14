[Previous](./[7]-Undoing-And-Rewriting-History.md) | [Table of Contents](./[0]-Introduction-to-Git.md) | [Next](./[9]-Workflows-And-Best-Practices.md)

# Lesson 8 - .gitignore And Housekeeping

## 8.1 What Is `.gitignore`?

A `.gitignore` file tells Git which files or folders to never track — things like build output, dependency folders, credentials, and OS-specific junk files.

Create it at the root of your repo:

```bash
touch .gitignore
```

---

## 8.2 Basic Pattern Syntax

```gitignore
# Comments start with a hash

# Ignore a specific file
secrets.env

# Ignore a whole folder
node_modules/
dist/

# Ignore by extension
*.log
*.tmp

# Ignore everywhere, matching in any directory
**/*.cache

# Negate a pattern (un-ignore something inside an ignored folder)
!important.log

# Ignore only in the root, not in subdirectories
/build

# Ignore a directory but keep a specific file in it
logs/*
!logs/.gitkeep
```

---

## 8.3 Common `.gitignore` Templates

Most languages/frameworks have well-known ignore patterns. GitHub maintains a large collection at [github.com/github/gitignore](https://github.com/github/gitignore). Example for Node.js:

```gitignore
node_modules/
npm-debug.log*
.env
dist/
coverage/
.DS_Store
```

---

## 8.4 Ignoring Files Already Tracked

`.gitignore` only affects **untracked** files. If a file is already tracked, adding it to `.gitignore` won't stop Git from tracking changes to it. You must untrack it first:

```bash
git rm --cached secrets.env
echo "secrets.env" >> .gitignore
git commit -m "Stop tracking secrets.env"
```

`--cached` removes it from Git's tracking but keeps the file on disk.

---

## 8.5 Global .gitignore (Across All Repos)

For things like OS/editor junk files you never want tracked in *any* repo:

```bash
git config --global core.excludesfile ~/.gitignore_global
```
```gitignore
# ~/.gitignore_global
.DS_Store
Thumbs.db
*.swp
.vscode/
.idea/
```

---

## 8.6 Checking Why a File Is Ignored

```bash
git check-ignore -v path/to/file
```

---

## 8.7 Other Housekeeping Commands

### `git gc` — Garbage Collection

Compresses and optimizes the repository's internal storage. Git runs this automatically sometimes, but you can trigger it manually:

```bash
git gc
git gc --aggressive   # deeper optimization, slower
```

### `git fsck` — File System Check

Checks the integrity of the repository's internal objects, finding corruption or dangling commits:

```bash
git fsck
```

### `.git/info/exclude`

A per-repo, non-shared alternative to `.gitignore` — useful for personal ignore rules you don't want to commit for the whole team.

```bash
echo "my-notes.txt" >> .git/info/exclude
```

### Repository Size

```bash
du -sh .git
git count-objects -vH
```

---

## 8.8 Summary

- `.gitignore` prevents untracked files from being accidentally committed.
- Already-tracked files need `git rm --cached` before ignoring takes effect.
- A global gitignore handles OS/editor clutter across every repo.
- `git gc` and `git fsck` keep the repository healthy and compact.

---

[Previous](./[7]-Undoing-And-Rewriting-History.md) | [Table of Contents](./[0]-Introduction-to-Git.md) | [Next](./[9]-Workflows-And-Best-Practices.md)
