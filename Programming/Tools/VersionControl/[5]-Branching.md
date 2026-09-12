[Previous](./[4]-Committing-Changes.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[6]-Merging.md)

*Tracking Changes*

# Lesson 5 - Branching

## 5.1 What Is A Branch

A branch is an independent, movable pointer to a line of development within a repository — practically, it lets you diverge from the main line of a project and work on something (a new feature, an experiment, a bug fix) without affecting that main line until you're ready to bring the work back together. Every Git repository starts with a default branch (commonly named `main`), and creating a new branch is extremely cheap, since a branch is really just a lightweight pointer to a specific commit rather than a full copy of the project.

```
        C ── D   (feature/dark-mode)
       /
A ── B
       \
        E ── F   (main)
```

Both branches share the same history up through commit `B`, then diverge — work on `feature/dark-mode` doesn't touch `main` at all until the two are explicitly brought back together.

---

## 5.2 Creating And Switching Branches

A new branch is created with `git branch <name>`, and you switch your working directory to a branch with `git switch <name>` (or the older `git checkout <name>`). The shorthand `git switch -c <name>` creates and switches to a new branch in one step. Once on a branch, any commits you make advance that branch's pointer forward, while other branches remain exactly where they were — this is what allows multiple independent lines of work to exist and progress simultaneously within the same repository.

```bash
git branch feature/dark-mode      # create the branch
git switch feature/dark-mode      # switch to it

# or in one step:
git switch -c feature/dark-mode

git branch                        # list local branches, current one marked with *
```

---

## 5.3 Branching Strategies

Teams adopt different conventions for how branches are used and named to keep a growing project organized. A common pattern uses a stable `main` branch that always reflects production-ready code, with short-lived feature branches (often named like `feature/login-page` or `fix/header-bug`) created for each piece of work and merged back once complete. Other teams use longer-lived branches for ongoing releases or environments (like `develop` or `staging`). The right strategy depends on team size, release cadence, and how much parallel work is happening at once — Lesson 8 covers several concrete collaborative workflows built on these ideas.

Some example branch names you might see in a real repository:

```
main
develop
feature/dark-mode
feature/PROJ-482-export-csv
fix/header-overlap-on-mobile
hotfix/expired-payment-token
```

---

## 5.4 Deleting And Renaming Branches

Once a branch's work has been merged and is no longer needed, it can be deleted with `git branch -d <name>`, which Git will refuse to do if the branch contains commits not yet merged elsewhere, as a safety check (the `-D` flag forces deletion regardless). Branches can be renamed with `git branch -m <old-name> <new-name>`, useful for correcting a typo or updating a naming convention. Deleting a branch only removes the pointer, not the commits themselves, as long as those commits are still reachable from another branch — but an unmerged branch's commits can become difficult to find again once deleted, so it's worth double-checking before force-deleting one.

```bash
git branch -d feature/dark-mode      # safe delete, refuses if unmerged
git branch -D feature/experiment     # force delete, even if unmerged
git branch -m fix/hedaer-bug fix/header-bug   # rename to fix a typo
```

[Previous](./[4]-Committing-Changes.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[6]-Merging.md)
