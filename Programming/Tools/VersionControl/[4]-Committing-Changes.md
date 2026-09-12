[Previous](./[3]-Getting-Started-With-Git.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[5]-Branching.md)

*Tracking Changes*

# Lesson 4 - Committing Changes

## 4.1 Staging Changes

Before a change can be committed, it must be staged with `git add`, which tells Git exactly which modifications should be included in the next snapshot. You can stage a single file (`git add file.txt`), several files, or every change in the working directory at once (`git add .`). Staging is a deliberate, separate step from editing precisely so you can build focused, logical commits — for example, staging and committing just the bug fix you finished, while leaving unrelated work-in-progress changes unstaged for later.

```bash
git add fix.py            # stage one file
git add fix.py tests.py   # stage a few specific files
git add .                 # stage everything changed in the current directory
git add -p                # interactively choose which hunks of a file to stage
```

`git add -p` is especially useful when a single file has both a finished fix and an unrelated experiment mixed together — it lets you stage just the finished part, line by line.

---

## 4.2 Writing Good Commit Messages

A commit message explains what changed and, more importantly, why — the diff itself already shows what lines changed, so a good message adds the context a diff can't. A widely followed convention is a short summary line (under about 50 characters, written in the imperative mood, like "Fix null pointer in login handler") optionally followed by a blank line and a more detailed explanation of the reasoning or trade-offs behind the change. Clear commit messages turn a project's history into a readable narrative that future contributors (including your future self) can actually learn from, rather than a series of unhelpful entries like "fix stuff" or "wip".

Compare these two commit messages for the exact same change:

```
# Unhelpful
fix stuff

# Helpful
Fix null pointer in login handler

The session token was read before checking whether the user
was authenticated, causing a crash for logged-out users
hitting the dashboard route directly.
```

The second version tells a future reader exactly what broke and why, without needing to reconstruct it from the diff alone.

---

## 4.3 Viewing History

`git log` displays a repository's commit history, showing each commit's unique hash, author, date, and message, most recent first. Common variations include `git log --oneline` for a condensed one-line-per-commit view, and `git log --graph` to visualize how branches diverged and merged over time. `git show <commit>` displays the full details and diff of a single specific commit, and `git blame <file>` shows which commit last modified each line of a file — extremely useful for tracking down when and why a particular piece of code was introduced.

```
$ git log --oneline
a1b2c3d Fix null pointer in login handler
7e8f9a0 Add discount code support to checkout
3c4d5e6 Initial payment processing module

$ git blame login.py
7e8f9a0 (Ada Lovelace 2026-09-08) def handle_login(request):
a1b2c3d (Ada Lovelace 2026-09-09)     if not request.user.is_authenticated:
```

---

## 4.4 Undoing And Amending Commits

Mistakes in a commit are common and easily fixed. `git commit --amend` lets you modify the most recent commit — updating its message, or adding staged changes you forgot to include — replacing it with a corrected version rather than creating a new commit on top. This is safe and commonly used for commits that haven't been shared with anyone else yet; amending or otherwise rewriting commits that have already been pushed to a shared remote requires more care, a topic covered fully in Lesson 14.

```bash
git commit --amend -m "Fix null pointer in login handler (typo)"

# forgot to include a file? stage it, then amend without changing the message:
git add forgotten_file.py
git commit --amend --no-edit
```

[Previous](./[3]-Getting-Started-With-Git.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[5]-Branching.md)
