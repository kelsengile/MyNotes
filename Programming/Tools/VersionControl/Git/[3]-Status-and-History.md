[Previous](./[2]-Git-Basics.md) | [Table of Contents](./[0]-Introduction-to-Git.md) | [Next](./[4]-Branching.md)

# Lesson 3 - Checking Status And Viewing History

## 3.1 `git status`

Shows the current state of your working directory and staging area: what's modified, what's staged, and what's untracked.

```bash
git status
```

Example output:
```
On branch main
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   index.html

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
        modified:   style.css

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        notes.txt
```

Short format for a more compact view:
```bash
git status -s
```
```
M  index.html
 M style.css
?? notes.txt
```
- First column = staged state, second column = working directory state.
- `M` = modified, `A` = added, `D` = deleted, `??` = untracked.

---

## 3.2 `git diff`

Shows the actual line-by-line changes.

```bash
git diff                # unstaged changes (working dir vs staging area)
git diff --staged       # staged changes (staging area vs last commit)
git diff HEAD           # all changes (working dir vs last commit)
git diff file.txt       # diff for one specific file
```

---

## 3.3 `git log`

Shows commit history.

```bash
git log
```
```
commit a1b2c3d4e5f6...
Author: Jane Doe <jane@example.com>
Date:   Tue Jul 22 10:15:00 2026 +0800

    Fix pagination bug
```

### Useful `git log` variants

```bash
git log --oneline                # one line per commit
git log --oneline --graph        # ASCII graph of branches/merges
git log --oneline --graph --all  # include all branches
git log -p                       # show full diff for each commit
git log -n 5                     # show last 5 commits
git log --author="Jane"          # filter by author
git log --since="2 weeks ago"    # filter by date
git log --grep="bugfix"          # filter by commit message content
git log -- file.txt              # history of a specific file
```

### Pretty formatting
```bash
git log --pretty=format:"%h - %an, %ar : %s"
```
Common placeholders: `%h` short hash, `%an` author name, `%ar` relative date, `%s` subject.

---

## 3.4 `git show`

Shows the details and diff of a single commit.

```bash
git show a1b2c3d
git show HEAD          # the most recent commit
git show HEAD~2        # two commits before the most recent
```

---

## 3.5 Referring to Commits

| Reference | Meaning |
|---|---|
| `HEAD` | The current commit you're on |
| `HEAD~1` or `HEAD^` | One commit before HEAD |
| `HEAD~3` | Three commits before HEAD |
| `a1b2c3d` | A specific commit hash (short form) |
| `main` | The tip of the `main` branch |

---

## 3.6 Summary

- `git status` — what's changed right now, staged vs. unstaged.
- `git diff` — line-by-line changes; add `--staged` to see staged changes.
- `git log` — the commit history; `--oneline --graph` is a great daily-driver combo.
- `git show` — details of one specific commit.

---

[Previous](./[2]-Git-Basics.md) | [Table of Contents](./[0]-Introduction-to-Git.md) | [Next](./[4]-Branching.md)
