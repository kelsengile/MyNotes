[Previous](./[17]-Aliases-and-Config.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[19]-Git-Internals.md)

# Lesson 18 - Pull Requests And Code Review Workflow

## 18.1 What Is a Pull Request?

A Pull Request (PR) — called a Merge Request (MR) on GitLab — is a request to merge changes from one branch into another, opened on a hosting platform (GitHub, GitLab, Bitbucket). It's not a native Git concept — Git itself only knows about branches and commits — but it's the standard collaboration layer built on top of Git by these platforms.

A PR provides a space to:
- Review the actual diff of proposed changes.
- Leave inline comments and suggestions.
- Run automated checks (CI: tests, linting, builds).
- Discuss and iterate before merging.

---

## 18.2 Typical PR Workflow

```bash
git switch main
git pull
git switch -c feature/add-search
# ... make changes, commit ...
git push -u origin feature/add-search
```

Then, on the hosting platform:
1. Open a Pull Request from `feature/add-search` into `main`.
2. Fill in a title and description explaining what and why.
3. Request reviewers.
4. Wait for CI checks and review feedback.

---

## 18.3 Writing a Good PR Description

A useful PR description typically includes:
- **What** changed and **why** (the problem being solved).
- **How** it was tested.
- Any **screenshots** for UI changes.
- Links to related issues/tickets (e.g. `Closes #123`).

---

## 18.4 Responding to Review Feedback

```bash
# make requested changes
git add .
git commit -m "Address review feedback: extract validation helper"
git push
```
The PR updates automatically with the new commits — no need to close and reopen.

### Squashing Feedback Commits Before Merge

Many teams prefer a clean final history. You can either:
- Let the platform **squash-merge** automatically (combines all PR commits into one on merge).
- Manually clean up with interactive rebase before merging:
  ```bash
  git rebase -i main
  git push --force-with-lease
  ```

---

## 18.5 Merge Strategies on Hosting Platforms

| Strategy | Result |
|---|---|
| **Merge commit** | Preserves all individual commits + adds a merge commit |
| **Squash and merge** | Combines all PR commits into a single commit on the target branch |
| **Rebase and merge** | Replays PR commits individually onto the target branch, no merge commit |

Choice usually depends on team preference for history granularity vs. cleanliness.

---

## 18.6 Keeping a PR Up to Date with `main`

If `main` has moved on since you branched:

```bash
git switch feature/add-search
git fetch origin
git merge origin/main        # or: git rebase origin/main
git push
```
(Use `--force-with-lease` after a rebase, since it rewrites your branch's history.)

---

## 18.7 Code Review Best Practices

### As a Reviewer
- Review for correctness, readability, and maintainability — not just style (let linters handle style).
- Ask questions rather than issuing commands: "What happens if `items` is empty here?" rather than "This is wrong."
- Approve promptly for small, low-risk changes; reserve deep scrutiny for complex or risky ones.
- Distinguish blocking issues from optional suggestions (many teams use prefixes like "nit:" for non-blocking style comments).

### As an Author
- Keep PRs small and focused on one thing — large PRs get slower, shallower reviews.
- Self-review your own diff before requesting review; catch typos and leftover debug code yourself.
- Respond to every comment, even if just to say "Done" or explain why you didn't change something.
- Don't take feedback personally — it's about the code, not you.

---

## 18.8 Draft Pull Requests

Most platforms support marking a PR as a "Draft" — signals it's a work in progress, not ready for full review, while still allowing early feedback and running CI.

---

## 18.9 Linking Issues

Most platforms auto-close linked issues when the PR merges, using keywords in the description:
```
Closes #42
Fixes #17
Resolves #8
```

---

## 18.10 Summary

- PRs/MRs are a platform-level collaboration workflow built on top of Git branches — not a native Git feature.
- Small, well-described PRs with clear commit history get reviewed faster and more thoroughly.
- Merge strategy (merge commit / squash / rebase) determines how the final history looks — pick one convention per team.
- Keep your branch updated with the target branch via merge or rebase before merging.

---

[Previous](./[17]-Aliases-and-Config.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[19]-Git-Internals.md)
