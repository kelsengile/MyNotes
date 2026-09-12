[Previous](./[9]-Pull-Requests-And-Code-Review.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[11]-Stashing-And-Cherry-Picking.md)

*Advanced Techniques*

# Lesson 10 - Rebasing

## 10.1 What Is Rebasing

Rebasing is an alternative to merging for integrating changes from one branch into another. Instead of creating a merge commit that joins two diverged histories together (Lesson 6), `git rebase <branch>` takes the commits unique to your current branch and replays them, one by one, on top of the latest commit of `<branch>`, as if you had started your work from there in the first place. The result is a clean, linear history with no merge commits, as though your changes were written after all the other work had already happened.

---

## 10.2 Rebase vs Merge

Merging preserves the true, exact history of when and how branches diverged and came back together, at the cost of a history that can become cluttered with merge commits, especially on a busy project. Rebasing produces a simpler, linear history that's often easier to read and navigate, but it does so by literally rewriting commits — each replayed commit gets a new identity, even though its content is the same — which means it changes history rather than just adding to it. Neither approach is universally correct; many teams use rebase to keep local, unpublished work clean before merging, while relying on regular merges (via pull requests) for actually integrating finished work into a shared branch.

---

## 10.3 Interactive Rebase

Interactive rebase (`git rebase -i`) goes further than simply replaying commits — it opens an editable list of the commits about to be replayed and lets you reorder them, edit their messages, combine several commits into one ("squash"), split a commit apart, or drop a commit entirely. This is a powerful tool for cleaning up a messy sequence of work-in-progress commits into a small number of clear, well-organized ones before sharing that work with others, turning something like "wip", "fix typo", "actually fix it" into a single clean commit that tells a coherent story.

---

## 10.4 The Golden Rule Of Rebasing

Because rebasing rewrites commits and gives them new identities, it's dangerous to rebase commits that other people have already pulled and built work on top of — doing so creates duplicate, conflicting versions of the same commits in different people's histories, leading to confusing and painful conflicts. This is often summarized as the golden rule of rebasing: never rebase commits that have already been pushed and shared publicly; only rebase your own local, unpublished commits. Following this rule lets you freely use rebase to keep your own work clean, while avoiding the history chaos it can cause on shared branches.

[Previous](./[9]-Pull-Requests-And-Code-Review.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[11]-Stashing-And-Cherry-Picking.md)
