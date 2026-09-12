[Previous](./[13]-Undoing-Changes.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[15]-Submodules-And-Monorepos.md)

*Maintaining History*

# Lesson 14 - Rewriting History

## 14.1 Amending Commits

As covered briefly in Lesson 4, `git commit --amend` replaces the most recent commit with a new version, whether you're fixing a typo in the message or adding a forgotten change. This is the smallest, safest form of history rewriting, since it only touches the single most recent commit and, like all rewriting, is only safe to do before that commit has been shared with anyone else.

```bash
git commit --amend -m "Fix rounding error in tax calculation"
```

---

## 14.2 Squashing Commits

Squashing combines several consecutive commits into a single one, typically done through an interactive rebase (Lesson 10) by marking commits to be "squashed" into the one before them. This is commonly used to clean up a messy sequence of small, incremental commits — made naturally while working through a problem — into one polished commit (or a small handful) before merging that work into a shared branch. Many hosting platforms also offer a one-click "squash and merge" option when merging a pull request, automatically combining an entire branch's commits into one on the target branch.

```
Before:                              After squashing:
  wip
  fix typo                    -->     Fix rounding error in tax calculation
  actually fix it
```

---

## 14.3 Filtering And Rewriting Large Histories

Occasionally a project needs more drastic history surgery than amending or squashing — for example, removing a large binary file or an accidentally committed secret from every commit that ever contained it, not just the most recent one. Tools purpose-built for this, like `git filter-repo`, can rewrite an entire repository's history according to specified rules, effectively rebuilding every affected commit from scratch. This is a heavyweight, disruptive operation that changes the identity of every rewritten commit and everything built on top of it, so it's reserved for serious situations rather than everyday cleanup.

```bash
# remove a file from every commit in the entire history
git filter-repo --path secrets.env --invert-paths
```

---

## 14.4 Risks Of Rewriting Shared History

Every history-rewriting operation in this lesson shares the same fundamental risk described by the golden rule of rebasing: once a commit has been pushed and someone else has pulled it, rewriting that commit creates two different, conflicting versions of the same history in different people's local repositories. Reconciling that divergence typically requires everyone affected to manually intervene, discard their local copies of the affected branch, and re-sync — a disruptive process best avoided entirely by only rewriting commits that haven't yet been shared, and treating any history rewrite of a shared branch as something that requires clear communication with the whole team beforehand.

```
Your machine:      A ── B ── C'   (rewritten)
Teammate's machine: A ── B ── C   (original — now conflicts with yours)
```

[Previous](./[13]-Undoing-Changes.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[15]-Submodules-And-Monorepos.md)
