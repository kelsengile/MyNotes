[Previous](./%5B1%5D-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-Git.md) | [Next](./%5B3%5D-Status-and-History.md)

# Lesson 2 - Git Basics

## 2.1 The Three (Four) Areas of Git

Understanding these areas is the key to understanding almost every Git command:

1. **Working Directory** – the actual files on your disk that you edit.
2. **Staging Area (Index)** – a "draft" area where you place changes you want to include in your next commit.
3. **Repository (.git)** – where committed history is permanently stored.
4. **Remote** – a copy of the repository hosted elsewhere (e.g. GitHub).

```
Working Directory  --git add-->  Staging Area  --git commit-->  Repository  --git push-->  Remote
```

---

## 2.2 Creating a Repository

### Starting a new repo
```bash
mkdir my-project
cd my-project
git init
```

This creates a hidden `.git` folder that holds all of Git's tracking data.

### Cloning an existing repo
```bash
git clone https://github.com/user/repo.git
```

This downloads the full history and checks out the default branch.

---

## 2.3 The Basic Workflow

1. Edit files in your working directory.
2. Stage the changes you want to commit.
3. Commit the staged changes with a message.

```bash
echo "Hello world" > hello.txt
git add hello.txt
git commit -m "Add hello.txt"
```

### `git add`

Moves changes from the working directory into the staging area.

```bash
git add file.txt        # stage one file
git add file1 file2     # stage multiple files
git add .                # stage everything in the current directory
git add -A               # stage everything in the whole repo, including deletions
```

### `git commit`

Takes everything in the staging area and saves it as a permanent snapshot in the repository's history.

```bash
git commit -m "Short descriptive message"
```

Skip staging and commit all tracked, modified files directly (does NOT include new/untracked files):
```bash
git commit -am "Message"
```

---

## 2.4 Writing Good Commit Messages

- Keep the first line under ~50 characters, written in the imperative mood: "Fix bug" not "Fixed bug" or "Fixes bug".
- Add a blank line, then a longer explanation if needed.

```
Fix off-by-one error in pagination

The previous implementation skipped the last item on
the final page when the page size didn't evenly divide
the total count.
```

---

## 2.5 Tracked vs. Untracked Files

- **Untracked**: files Git doesn't know about yet (new files you haven't `git add`ed).
- **Tracked**: files Git is watching for changes — these can be *unmodified*, *modified*, or *staged*.

---

## 2.6 A Minimal Example Session

```bash
git init
echo "# My Project" > README.md
git add README.md
git commit -m "Initial commit"
```

---

## 2.7 Summary

- Git tracks snapshots, not diffs, though it displays changes as diffs.
- Everything flows: working directory → staging area → repository.
- `git init` starts a new repo; `git clone` copies an existing one.
- `git add` stages, `git commit` saves a permanent snapshot.

---

[Previous](./%5B1%5D-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-Git.md) | [Next](./%5B3%5D-Status-and-History.md)
