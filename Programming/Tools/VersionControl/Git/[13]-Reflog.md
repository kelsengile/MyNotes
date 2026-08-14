[Previous](./[12]-Tags-and-Cherry-Picking.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[14]-Submodules.md)

# Lesson 13 - Reflog - Recovering Lost Commits

## 13.1 What Is the Reflog?

The reflog ("reference log") is a local, chronological record of everywhere `HEAD` (and branch tips) have pointed in your repository — every commit, checkout, reset, rebase, and merge. It's your safety net for "I think I just destroyed my work."

Importantly: the reflog is **local only** — it's not pushed to remotes and not cloned by others. It's specific to your machine's copy of the repo.

---

## 13.2 Viewing the Reflog

```bash
git reflog
```
```
a1b2c3d (HEAD -> main) HEAD@{0}: commit: Add payment validation
e4f5g6h HEAD@{1}: reset: moving to HEAD~1
h7i8j9k HEAD@{2}: commit: Fix typo
h7i8j9k HEAD@{3}: rebase (finish): returning to refs/heads/main
```

Each entry shows: the commit hash, `HEAD@{N}` (how many steps back in time), and what action produced that state.

### Reflog for a Specific Branch

```bash
git reflog show feature-branch
```

---

## 13.3 Recovering a "Lost" Commit

### Scenario: You ran `git reset --hard` and lost commits

```bash
git reflog
```
Find the entry just before the reset — e.g. `e4f5g6h HEAD@{1}: commit: Add feature X`.

```bash
git reset --hard e4f5g6h
```
Or, without discarding your current state, just look at it first:
```bash
git show e4f5g6h
git checkout e4f5g6h
```

### Scenario: You deleted a branch by mistake

```bash
git branch -D feature-branch
git reflog
# find the last commit that branch pointed to, e.g. a1b2c3d
git branch feature-branch a1b2c3d
```

### Scenario: A rebase went wrong

```bash
git reflog
# find the entry right before "rebase (start)"
git reset --hard HEAD@{5}
```

---

## 13.4 Using `HEAD@{N}` Directly

You can reference reflog entries directly in most commands:

```bash
git diff HEAD@{2} HEAD
git checkout HEAD@{1}
```

Time-based references also work:
```bash
git show HEAD@{yesterday}
git show main@{2.weeks.ago}
```

---

## 13.5 Finding Dangling Commits

If a commit isn't referenced by any branch, tag, or reflog entry, it becomes a "dangling" (unreachable) object, eventually cleaned up by `git gc`. You can find dangling commits before that happens:

```bash
git fsck --lost-found
```

---

## 13.6 Reflog Expiration

By default, reflog entries expire after 90 days (unreachable ones after 30) — controlled by:
```bash
git config gc.reflogExpire
git config gc.reflogExpireUnreachable
```

---

## 13.7 What the Reflog Can't Save You From

- It won't help if you `git clone` a fresh copy — the reflog isn't part of a clone.
- It only tracks *local* ref movements — a `git push --force` that overwrites a remote branch isn't recorded on the remote's side unless the remote has its own hooks/backups.
- It eventually expires (default: 90 days for reachable entries).

---

## 13.8 Summary

- The reflog records every place `HEAD` and branches have pointed to locally — a built-in undo history.
- `git reflog` to view it, then `git reset --hard <hash>` or `git checkout <hash>` to recover.
- It's local-only and does expire, so it's a safety net, not a permanent backup.

---

[Previous](./[12]-Tags-and-Cherry-Picking.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[14]-Submodules.md)
