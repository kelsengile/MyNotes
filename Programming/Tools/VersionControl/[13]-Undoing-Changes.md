[Previous](./[12]-Tags-And-Releases.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[14]-Rewriting-History.md)

*Maintaining History*

# Lesson 13 - Undoing Changes

## 13.1 Reset vs Revert vs Checkout

Git offers several distinct tools for undoing changes, and choosing the right one matters. `git checkout` (or the newer `git restore`) discards uncommitted changes in the working directory, reverting specific files back to their last committed state. `git reset` moves the current branch pointer backward to an earlier commit, effectively undoing commits as if they never happened — powerful, but it rewrites history and should generally be reserved for local, unpublished commits. `git revert` creates a brand-new commit that undoes the changes of an earlier commit, leaving the original commit intact in history — this is the safe way to undo a commit that has already been shared with others.

| Tool | Undoes | Rewrites history? | Safe on shared commits? |
|---|---|---|---|
| `git restore <file>` | Uncommitted edits | No | N/A (never committed) |
| `git reset <commit>` | Commits | Yes | No |
| `git revert <commit>` | Commits | No — adds a new commit | Yes |

```bash
git restore app.py             # discard uncommitted edits to app.py
git reset HEAD~1               # move main back one commit (local only!)
git revert a1b2c3d             # safely undo a shared commit with a new commit
```

---

## 13.2 Soft, Mixed, And Hard Reset

`git reset` has three modes that control what happens to the changes being undone. A **soft reset** moves the branch pointer back but leaves all the undone changes staged, ready to be recommitted differently. A **mixed reset** (the default) moves the pointer back and unstages the changes, but leaves them present in the working directory as uncommitted edits. A **hard reset** moves the pointer back and discards the changes entirely, restoring the working directory to exactly match the target commit — this is the only mode that actually deletes work, so it should be used carefully, since discarded uncommitted changes generally cannot be recovered.

```bash
git reset --soft HEAD~1     # changes end up staged, ready to recommit
git reset HEAD~1            # (mixed, default) changes end up unstaged, still on disk
git reset --hard HEAD~1     # changes are gone entirely — use with caution
```

---

## 13.3 Reverting Public Commits

Once a commit has been pushed and shared, using `git reset` to remove it would rewrite history that others already have, creating exactly the kind of divergence the golden rule of rebasing warns against (Lesson 10). `git revert <commit>` avoids this problem entirely: rather than erasing the commit, it analyzes what that commit changed and creates a new commit that applies the opposite change, undoing its effect while keeping the full history — including the mistake and its correction — visible and intact. This makes revert the appropriate tool any time you need to undo work that other people may have already built on.

```
$ git revert a1b2c3d
$ git log --oneline
9f8e7d6 Revert "Add discount code support to checkout"
a1b2c3d Add discount code support to checkout
```

Both the original mistake and its correction remain fully visible in history.

---

## 13.4 Recovering Lost Work With Reflog

Even after a hard reset or a rebase that seems to have discarded commits, Git rarely deletes data immediately — the reflog (`git reflog`) keeps a local record of every position the branch pointers (like `HEAD`) have recently been at, even ones no longer reachable from any current branch. If you discover you've lost commits you actually needed, the reflog often lets you find the commit hash they were at and recover them with `git reset` or `git cherry-pick` back onto a branch. The reflog is local to your machine and expires after a period of time, so it's a safety net for recent mistakes rather than a permanent archive, but it has rescued countless developers from what looked like unrecoverable errors.

```
$ git reflog
7e8f9a0 HEAD@{0}: reset: moving to HEAD~1
a1b2c3d HEAD@{1}: commit: Add discount code support to checkout

$ git reset --hard a1b2c3d      # recover the "lost" commit
```

[Previous](./[12]-Tags-And-Releases.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[14]-Rewriting-History.md)
