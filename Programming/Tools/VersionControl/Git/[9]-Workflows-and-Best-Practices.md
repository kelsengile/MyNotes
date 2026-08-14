[Previous](./[8]-Gitignore.md) | [Table of Contents](./[0]-Introduction-to-Git.md) | [Next](./[10]-Rebasing.md)

# Lesson 9 - Common Workflows And Best Practices

## 9.1 Popular Branching Models

### Feature Branch Workflow

The simplest and most common model: `main` is always deployable, all work happens on short-lived feature branches merged back in via pull requests.

```bash
git switch main
git pull
git switch -c feature/checkout-flow
# ... work, commit ...
git push -u origin feature/checkout-flow
# open a Pull Request, review, merge
```

### Git Flow

A heavier model with dedicated long-lived branches:

- `main` — production-ready releases only
- `develop` — integration branch for ongoing work
- `feature/*` — individual features, branched from and merged into `develop`
- `release/*` — release preparation, branched from `develop`, merged into both `main` and `develop`
- `hotfix/*` — urgent production fixes, branched from `main`

Better suited to projects with scheduled releases than to continuously-deployed web apps.

### Trunk-Based Development

Everyone commits frequently (often multiple times a day) to a single `main`/`trunk` branch, using very short-lived branches or feature flags instead of long-lived feature branches. Common in teams practicing continuous deployment.

### GitHub Flow

A simplified feature-branch model: `main` is deployable at all times, branches are created for any change, opened as a Pull Request, reviewed, and merged — then immediately deployed.

---

## 9.2 Commit Best Practices

- **Commit often, in logical chunks.** Each commit should represent one coherent change, not "end of day save."
- **Write clear messages** in the imperative mood ("Add", "Fix", "Remove" — not "Added", "Fixing").
- **Don't commit generated files, secrets, or dependencies** — use `.gitignore`.
- **Keep commits buildable.** Ideally, every commit should leave the project in a working state (helps `bisect` and `revert`).

---

## 9.3 Pull Request / Code Review Best Practices

- Keep PRs small and focused — easier to review, easier to revert if something goes wrong.
- Write a description explaining *why*, not just *what*.
- Rebase or squash messy work-in-progress commits before requesting review.
- Respond to review comments with follow-up commits, then squash-merge if your team prefers linear history.

See [Pull Requests & Code Review Workflow](./[18]-Pull-Requests.md) for more detail.

---

## 9.4 Handling Merge Conflicts Gracefully

- Pull/rebase from `main` frequently to avoid large divergences.
- Communicate about files likely to conflict (e.g. shared config files).
- When conflicts do happen, resolve deliberately — don't just accept "theirs" or "ours" blindly without understanding the change.

---

## 9.5 Protecting Important Branches

On hosting platforms (GitHub, GitLab), configure branch protection rules for `main`:
- Require pull request review before merging.
- Require status checks (CI) to pass.
- Disallow force-pushes and direct pushes.

---

## 9.6 Semantic / Conventional Commits (Optional Convention)

Some teams adopt a structured commit message format to enable automated changelog generation:

```
feat: add user authentication
fix: correct off-by-one error in pagination
docs: update README installation steps
chore: bump dependency versions
refactor: extract validation logic into helper
```

---

## 9.7 General Tips

- **Never force-push to shared branches** without team agreement — use `--force-with-lease` if you must.
- **Tag releases** so you can always find and reproduce a specific shipped version (see [Tags & Cherry-Picking](./[12]-Tags-And-Cherry-Picking.md)).
- **Use `.gitattributes`** for consistent line endings across OSes:
  ```
  * text=auto
  ```
- **Automate checks** with hooks or CI so broken code never reaches `main` (see [Hooks](./[15]-Hooks.md)).

---

## 9.8 Summary

- Choose a branching model that matches your release cadence: Feature Branch/GitHub Flow for continuous delivery, Git Flow for scheduled releases, trunk-based for high-frequency integration.
- Small, focused, well-described commits and PRs make history useful instead of noisy.
- Protect `main`, review before merging, and never rewrite shared history without care.

---

[Previous](./[8]-Gitignore.md) | [Table of Contents](./[0]-Introduction-to-Git.md) | [Next](./[10]-Rebasing.md)
