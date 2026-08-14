[Previous](./[6]-Remotes-Push-And-Pull.md) | [Table of Contents](./[0]-Introduction-to-Git.md) | [Next](./[8]-Gitignore.md)

# Lesson 7 - Undoing Changes And Rewriting History

Git gives you many ways to "undo" things, depending on exactly what you want to undo and whether it's already been committed or pushed.

## 7.1 Undoing Uncommitted Changes

### Discard changes in the working directory
```bash
git restore file.txt          # modern syntax
git checkout -- file.txt       # older syntax
```

### Unstage a file (keep the changes, just remove from staging)
```bash
git restore --staged file.txt
git reset file.txt              # older syntax
```

### Discard *all* uncommitted changes
```bash
git restore .
git reset --hard      # also discards staged changes
```

### Remove untracked files
```bash
git clean -n     # dry run — show what would be deleted
git clean -f      # actually delete untracked files
git clean -fd     # also delete untracked directories
```

---

## 7.2 Undoing Commits

### `git commit --amend`

Modifies the most recent commit — change the message, or add forgotten files.

```bash
git commit --amend -m "New message"

# add a forgotten file to the last commit:
git add forgotten-file.txt
git commit --amend --no-edit
```
⚠️ Only amend commits that haven't been pushed/shared, since it rewrites the commit hash.

### `git reset`

Moves the current branch pointer to a different commit. Three modes:

```bash
git reset --soft HEAD~1    # undo commit, keep changes staged
git reset --mixed HEAD~1   # undo commit, keep changes unstaged (default)
git reset --hard HEAD~1    # undo commit, discard changes entirely
```

| Mode | Working Dir | Staging Area | Commit |
|---|---|---|---|
| `--soft` | unchanged | unchanged | moved |
| `--mixed` (default) | unchanged | reset | moved |
| `--hard` | reset | reset | moved |

⚠️ `--hard` permanently discards uncommitted work. Use with caution.

### `git revert`

Creates a **new** commit that undoes the changes of a previous commit — safe for shared/pushed history since it doesn't rewrite anything.

```bash
git revert a1b2c3d
git revert HEAD          # revert the most recent commit
git revert HEAD~2..HEAD  # revert a range of commits
```

---

## 7.3 `reset` vs. `revert` — Which to Use?

| | `reset` | `revert` |
|---|---|---|
| Rewrites history | Yes | No |
| Safe for pushed/shared commits | No | Yes |
| Use case | Local cleanup before sharing | Undoing something already public |

**Rule of thumb: never rewrite history that others may have already pulled.**

---

## 7.4 Rewriting History (Advanced)

### `git commit --amend` on older commits — use interactive rebase instead
See [Rebasing & Interactive Rebase](./[10]-Rebasing.md) for reordering, squashing, editing, and dropping past commits.

### `git filter-branch` / `git filter-repo`

Used to rewrite history across many commits (e.g. removing a sensitive file that was accidentally committed everywhere). `git filter-repo` is the modern, recommended tool (a separate install, faster and safer than the built-in `filter-branch`).

```bash
git filter-repo --path secrets.txt --invert-paths
```
⚠️ This rewrites every commit hash downstream. Never do this on shared history without coordinating with your team.

---

## 7.5 Recovering "Lost" Work

If you `reset --hard` or delete a branch by mistake, the commits often aren't actually gone right away — see [Reflog: Recovering Lost Commits](./[13]-Reflog.md).

---

## 7.6 Summary

- Undo *uncommitted* changes with `restore`/`checkout`/`clean`.
- Undo the *last* commit's message or contents with `commit --amend`.
- Move the branch pointer with `reset` (soft/mixed/hard) — safe only for unpushed commits.
- Use `revert` to safely undo already-shared commits by adding a new counteracting commit.
- Large-scale history rewrites need `filter-repo` and team coordination.

---

[Previous](./[6]-Remotes-Push-And-Pull.md) | [Table of Contents](./[0]-Introduction-to-Git.md) | [Next](./[8]-Gitignore.md)
