[Previous](./[3]-Status-and-History.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[5]-Merging-and-Conflicts.md)

# Lesson 4 - Branching

## 4.1 What Is a Branch?

A branch is simply a movable pointer to a commit. The default branch is usually called `main` (historically `master`). Branches let you work on features, fixes, or experiments in isolation without affecting other work.

Internally, a branch is just a 41-byte file containing a commit hash — this is why creating branches in Git is nearly instantaneous, unlike some other version control systems.

---

## 4.2 Viewing Branches

```bash
git branch                # list local branches (current one marked with *)
git branch -r              # list remote-tracking branches
git branch -a              # list all branches, local and remote
git branch -v              # list branches with their latest commit
```

---

## 4.3 Creating a Branch

```bash
git branch feature-login
```

This creates the branch but does **not** switch to it.

---

## 4.4 Switching Branches

```bash
git switch feature-login       # modern syntax
git checkout feature-login     # older syntax, still widely used
```

### Create and switch in one step

```bash
git switch -c feature-login
git checkout -b feature-login
```

---

## 4.5 Renaming a Branch

```bash
git branch -m old-name new-name
git branch -m new-name          # rename current branch
```

---

## 4.6 Deleting a Branch

```bash
git branch -d feature-login     # safe delete (refuses if unmerged)
git branch -D feature-login     # force delete
```

---

## 4.7 Comparing Branches

```bash
git diff main..feature-login
git log main..feature-login --oneline
```

---

## 4.8 A Typical Feature Branch Workflow

```bash
git switch main
git switch -c feature-signup
# ... make changes, commit them ...
git add .
git commit -m "Add signup form"
git switch main
git merge feature-signup
git branch -d feature-signup
```

---

## 4.9 Branch Naming Conventions

Common team conventions (not enforced by Git itself):

```
feature/user-authentication
bugfix/pagination-off-by-one
hotfix/critical-security-patch
release/v2.1.0
```

---

## 4.10 What "Current Branch" Really Means

`HEAD` is a pointer to the branch you currently have checked out (which itself points to a commit). Moving `HEAD` around — via `switch`/`checkout` — is how you move between branches.

```
HEAD -> main -> commit c3
                  ^
                commit c2
                  ^
                commit c1
```

---

## 4.11 Detached HEAD State

If you check out a specific commit rather than a branch, you enter "detached HEAD" state — you're no longer on any branch.

```bash
git checkout a1b2c3d
```

Any commits made here can be lost once you switch away, unless you create a branch to keep them:
```bash
git switch -c rescue-branch
```

---

## 4.12 Summary

- Branches are lightweight, movable pointers to commits.
- `git switch`/`checkout` moves `HEAD` between branches.
- `-c`/`-b` creates and switches in one step.
- Deleting a branch just removes the pointer — the commits stay reachable if merged elsewhere.

---

[Previous](./[3]-Status-and-History.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[5]-Merging-and-Conflicts.md)
