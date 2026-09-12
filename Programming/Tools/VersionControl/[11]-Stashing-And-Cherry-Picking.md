[Previous](./[10]-Rebasing.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[12]-Tags-And-Releases.md)

*Advanced Techniques*

# Lesson 11 - Stashing And Cherry-Picking

## 11.1 Stashing Changes

Sometimes you need to switch away from work in progress without committing it — for example, to urgently fix a bug on another branch. `git stash` takes your uncommitted changes (both staged and unstaged) and sets them aside on a stack, restoring your working directory to a clean state matching the last commit. Later, `git stash pop` reapplies the most recently stashed changes and removes them from the stash stack, letting you pick up exactly where you left off. Multiple stashes can be kept simultaneously and listed with `git stash list`.

---

## 11.2 Cherry-Picking Commits

Cherry-picking applies the changes from a single specific commit onto your current branch, without merging or rebasing the entire branch it came from. Running `git cherry-pick <commit-hash>` takes just that one commit's changes and creates a new commit with the same content on your current branch. This is useful when you need one particular fix or feature from another branch without pulling in everything else that branch contains — for example, applying a critical bug fix from a feature branch directly onto a release branch.

---

## 11.3 When To Use Each

Stashing and cherry-picking solve different problems and are often used together. Stashing is about temporarily pausing your current, uncommitted work so you can switch context and come back to it later — it's a short-term, personal tool that never gets shared with others. Cherry-picking is about deliberately importing an already-committed, permanent piece of history from one branch onto another — it's a much more visible action that becomes part of the project's permanent record. Recognizing which situation you're in (interrupted work-in-progress vs. an already-finished commit you need elsewhere) determines which tool fits.

---

## 11.4 Common Pitfalls

Stashes can be forgotten and accumulate over time, since they don't appear anywhere in normal history views — it's worth periodically checking `git stash list` and clearing out stashes you no longer need. Cherry-picking, meanwhile, creates a brand-new commit with a different identity than the original, even though the content matches; cherry-picking the same change onto a branch that later merges with the original source can sometimes cause Git to treat the same logical change as a conflict between its two different commit identities. Both tools are safe and valuable when used deliberately, but are best reserved for the specific, targeted situations they're designed for rather than as a general substitute for normal committing, branching, and merging.

[Previous](./[10]-Rebasing.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[12]-Tags-And-Releases.md)
