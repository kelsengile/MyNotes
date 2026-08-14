[Previous](./[10]-Rebasing.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[12]-Tags-and-Cherry-Picking.md)

# Lesson 11 - Stashing

## 11.1 What Is Stashing?

`git stash` temporarily saves your uncommitted changes (both staged and unstaged) so you can switch context — e.g. to fix an urgent bug on another branch — without committing half-finished work.

---

## 11.2 Basic Usage

```bash
git stash
```
This saves your working directory + staging area changes and reverts them to match `HEAD`, giving you a clean working directory.

### Restoring Your Changes

```bash
git stash pop     # apply the most recent stash AND remove it from the stash list
git stash apply    # apply the most recent stash but KEEP it in the stash list
```

---

## 11.3 Naming and Managing Multiple Stashes

```bash
git stash save "WIP: refactoring auth module"
git stash push -m "WIP: refactoring auth module"   # modern equivalent of save
```

### Listing Stashes

```bash
git stash list
```
```
stash@{0}: On feature: WIP: refactoring auth module
stash@{1}: On main: WIP: experimenting with layout
```

### Applying a Specific Stash

```bash
git stash apply stash@{1}
git stash pop stash@{1}
```

### Viewing Stash Contents

```bash
git stash show stash@{0}          # summary of files changed
git stash show -p stash@{0}       # full diff
```

### Dropping Stashes

```bash
git stash drop stash@{0}   # delete one specific stash
git stash clear             # delete all stashes
```

---

## 11.4 Stashing Specific Files

```bash
git stash push -- file1.txt file2.txt
```

---

## 11.5 Including Untracked or Ignored Files

By default, `git stash` only stashes tracked file changes.

```bash
git stash -u        # also stash untracked files
git stash -a        # also stash untracked AND ignored files
```

---

## 11.6 Creating a Branch from a Stash

Useful when a stash's changes have diverged too much to apply cleanly:

```bash
git stash branch new-branch-name stash@{0}
```
This creates a new branch at the commit the stash was based on, checks it out, and applies the stash.

---

## 11.7 A Typical Scenario

```bash
# mid-way through a feature, urgent bug reported
git stash push -m "WIP: new checkout page"
git switch main
git switch -c hotfix/payment-bug
# ... fix bug, commit, push, merge ...
git switch feature/checkout-page
git stash pop
```

---

## 11.8 Stash Conflicts

Just like merges, applying a stash can conflict with the current state of your files. Git will mark the conflicting sections the same way as a merge conflict — resolve them, then `git add` the files. Note the stash entry itself is **not** automatically dropped if `pop` results in a conflict; drop it manually once resolved if needed.

---

## 11.9 Summary

- `git stash` shelves uncommitted work so you can switch tasks with a clean working directory.
- `pop` applies and removes the stash; `apply` applies but keeps it around.
- Multiple stashes are tracked as a stack (`stash@{0}`, `stash@{1}`, ...).
- Use `-u`/`-a` to include untracked/ignored files, and `git stash branch` when a stash no longer applies cleanly.

---

[Previous](./[10]-Rebasing.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[12]-Tags-and-Cherry-Picking.md)
