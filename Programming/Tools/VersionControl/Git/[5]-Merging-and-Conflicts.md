[Previous](./[4]-Branching.md) | [Table of Contents](./[0]-Introduction-to-Git.md) | [Next](./%5B6%5D-Remotes-Push-and-Pull.md)

# Lesson 5 - Merging And Resolving Conflicts

## 5.1 What Is a Merge?

Merging combines the changes from one branch into another. Git has two main strategies for this, chosen automatically based on the branch history.

---

## 5.2 Fast-Forward Merge

If the target branch hasn't diverged (no new commits since the feature branch was created), Git simply moves the pointer forward — no new commit is created.

```
Before:  main -> A
                  \
                   B -> C  (feature)

After merge (fast-forward):  main -> A -> B -> C
```

```bash
git switch main
git merge feature
```

To force a merge commit even when a fast-forward is possible:
```bash
git merge --no-ff feature
```

---

## 5.3 Three-Way Merge

If both branches have new commits, Git creates a new **merge commit** with two parents.

```
main:     A -> B -> D
                \     \
feature:         C --> M  (merge commit, has two parents)
```

```bash
git switch main
git merge feature
```

Git opens your editor for a merge commit message (auto-generated, usually fine to accept as-is).

---

## 5.4 Merge Conflicts

A conflict happens when the same lines were changed differently on both branches, and Git can't decide which version is correct.

```bash
git merge feature
```
```
Auto-merging index.html
CONFLICT (content): Merge conflict in index.html
Automatic merge failed; fix conflicts and then commit the result.
```

### Anatomy of a Conflict Marker

Git inserts markers directly into the affected file:

```
<<<<<<< HEAD
<h1>Welcome to our site</h1>
=======
<h1>Welcome to My Awesome Site</h1>
>>>>>>> feature
```

- Everything between `<<<<<<< HEAD` and `=======` is **your current branch's version**.
- Everything between `=======` and `>>>>>>> feature` is **the incoming branch's version**.

### Resolving a Conflict

1. Open the file and edit it to the correct final content, removing all the marker lines (`<<<<<<<`, `=======`, `>>>>>>>`).
2. Stage the resolved file:
   ```bash
   git add index.html
   ```
3. Complete the merge:
   ```bash
   git commit
   ```
   (Git pre-fills a merge commit message; you can just save and exit.)

### Checking Conflict Status Mid-Merge

```bash
git status
```
Lists files marked as "both modified" — these need resolving.

### Aborting a Merge

If things go wrong and you want to start over:
```bash
git merge --abort
```
This restores everything to the state before you ran `git merge`.

---

## 5.5 Using a Merge Tool

For complex conflicts, a visual tool is often easier than manual editing:

```bash
git mergetool
```
Configure your preferred tool (e.g. VS Code, Meld, KDiff3):
```bash
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd 'code --wait $MERGED'
```

---

## 5.6 Tips to Avoid Conflicts

- Pull/merge frequently so branches don't diverge too far.
- Keep feature branches short-lived.
- Communicate with teammates about who's touching which files.
- Break large changes into smaller, focused commits and PRs.

---

## 5.7 Summary

- Fast-forward merges just move a pointer; three-way merges create a merge commit.
- Conflicts appear as `<<<<<<<` / `=======` / `>>>>>>>` markers in the file — edit, stage, then commit.
- `git merge --abort` bails out cleanly if a merge goes sideways.

---

[Previous](./[4]-Branching.md) | [Table of Contents](./[0]-Introduction-to-Git.md) | [Next](./%5B6%5D-Remotes-Push-and-Pull.md)
