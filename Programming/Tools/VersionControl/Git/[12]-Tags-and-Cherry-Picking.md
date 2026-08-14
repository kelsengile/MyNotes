[Previous](./[11]-Stashing.md) | [Table of Contents](./[0]-Introduction-to-Git.md) | [Next](./[13]-Reflog.md)

# Lesson 12 - Tags And Cherry-Picking

## 12.1 Part 1: Tags

### What Is a Tag?

A tag is a permanent, named pointer to a specific commit — commonly used to mark release points (`v1.0.0`, `v2.3.1`). Unlike branches, tags don't move as new commits are added.

### Types of Tags

**Lightweight tags** — just a name pointing to a commit, like a branch that doesn't move:
```bash
git tag v1.0.0
```

**Annotated tags** — full objects stored in Git's database, including tagger name, date, message, and (optionally) a GPG signature. Recommended for releases.
```bash
git tag -a v1.0.0 -m "Release version 1.0.0"
```

### Tagging a Past Commit

```bash
git tag -a v1.0.0 a1b2c3d -m "Release version 1.0.0"
```

### Listing Tags

```bash
git tag
git tag -l "v1.*"     # filter by pattern
```

### Viewing Tag Details

```bash
git show v1.0.0
```

### Pushing Tags

Tags are **not** pushed automatically with `git push`.

```bash
git push origin v1.0.0        # push a single tag
git push origin --tags         # push all tags
```

### Deleting Tags

```bash
git tag -d v1.0.0                       # delete locally
git push origin --delete v1.0.0         # delete on remote
```

### Checking Out a Tag

```bash
git checkout v1.0.0
```
This puts you in detached HEAD state (see [Branching](./[4]-Branching.md)) — create a branch if you need to make changes from this point.

### Signed Tags

For verifying release authenticity via GPG:
```bash
git tag -s v1.0.0 -m "Signed release 1.0.0"
git tag -v v1.0.0     # verify
```

---

## 12.2 Part 2: Cherry-Picking

### What Is Cherry-Picking?

`git cherry-pick` applies the changes from one specific commit (from any branch) onto your current branch, as a new commit — without merging the entire branch.

### Basic Usage

```bash
git switch main
git cherry-pick a1b2c3d
```

This creates a new commit on `main` with the same changes and message as `a1b2c3d`, but with a new hash.

### Cherry-Picking Multiple Commits

```bash
git cherry-pick a1b2c3d e4f5g6h
git cherry-pick a1b2c3d^..e4f5g6h    # a range (exclusive of the first commit's parent)
```

### Cherry-Pick Without Committing

Useful if you want to review or combine changes before committing:
```bash
git cherry-pick -n a1b2c3d
# or --no-commit
```

### Handling Conflicts During Cherry-Pick

```bash
git cherry-pick a1b2c3d
# ... conflict occurs ...
# fix the conflicting file(s)
git add resolved-file.txt
git cherry-pick --continue

# or bail out
git cherry-pick --abort
```

### Common Use Cases

- **Hotfixes**: apply a critical fix from `main` onto a `release` branch without merging all of `main`'s other changes.
- **Recovering a commit** made on the wrong branch — cherry-pick it onto the correct one.
- **Selective backporting** of a feature to an older supported version.

### Cherry-Pick and Commit History

Because cherry-picking creates a *new* commit (with a new hash), the same logical change can appear twice in history (once on each branch) — this is expected and generally harmless, though tools like `git log --graph` may show it as separate, unrelated commits.

---

## 12.3 Summary

- **Tags** mark specific, fixed points in history — annotated tags are recommended for releases and aren't pushed by default.
- **Cherry-pick** copies a specific commit's changes onto another branch as a new commit, useful for hotfixes and selective backports.
- Both tools let you be surgical about which changes travel where, without needing a full merge.

---

[Previous](./[11]-Stashing.md) | [Table of Contents](./[0]-Introduction-to-Git.md) | [Next](./[13]-Reflog.md)
