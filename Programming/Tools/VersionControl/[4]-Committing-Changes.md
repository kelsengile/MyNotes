[Previous](./[3]-Getting-Started-With-Git.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[5]-Branching.md)

*Tracking Changes*

# Lesson 4 - Committing Changes

## 4.1 Staging Changes

Before a change can be committed, it must be staged with `git add`, which tells Git exactly which modifications should be included in the next snapshot. You can stage a single file (`git add file.txt`), several files, or every change in the working directory at once (`git add .`). Staging is a deliberate, separate step from editing precisely so you can build focused, logical commits — for example, staging and committing just the bug fix you finished, while leaving unrelated work-in-progress changes unstaged for later.

---

## 4.2 Writing Good Commit Messages

A commit message explains what changed and, more importantly, why — the diff itself already shows what lines changed, so a good message adds the context a diff can't. A widely followed convention is a short summary line (under about 50 characters, written in the imperative mood, like "Fix null pointer in login handler") optionally followed by a blank line and a more detailed explanation of the reasoning or trade-offs behind the change. Clear commit messages turn a project's history into a readable narrative that future contributors (including your future self) can actually learn from, rather than a series of unhelpful entries like "fix stuff" or "wip".

---

## 4.3 Viewing History

`git log` displays a repository's commit history, showing each commit's unique hash, author, date, and message, most recent first. Common variations include `git log --oneline` for a condensed one-line-per-commit view, and `git log --graph` to visualize how branches diverged and merged over time. `git show <commit>` displays the full details and diff of a single specific commit, and `git blame <file>` shows which commit last modified each line of a file — extremely useful for tracking down when and why a particular piece of code was introduced.

---

## 4.4 Undoing And Amending Commits

Mistakes in a commit are common and easily fixed. `git commit --amend` lets you modify the most recent commit — updating its message, or adding staged changes you forgot to include — replacing it with a corrected version rather than creating a new commit on top. This is safe and commonly used for commits that haven't been shared with anyone else yet; amending or otherwise rewriting commits that have already been pushed to a shared remote requires more care, a topic covered fully in Lesson 14.

[Previous](./[3]-Getting-Started-With-Git.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[5]-Branching.md)
