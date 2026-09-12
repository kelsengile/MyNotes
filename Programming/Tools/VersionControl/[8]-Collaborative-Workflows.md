[Previous](./[7]-Remote-Repositories.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[9]-Pull-Requests-And-Code-Review.md)

*Collaboration*

# Lesson 8 - Collaborative Workflows

## 8.1 Centralized Workflow

In the centralized workflow, every collaborator has push access to a single shared remote repository and commits directly to its main branch, much like a traditional centralized version control system, even though the underlying tool is distributed. This is simple to set up and understand, and works reasonably well for very small teams with a lot of trust and communication, but it offers little structure to prevent unfinished or broken work from landing directly on the branch everyone else depends on.

```bash
git pull origin main
# ... make changes directly ...
git add .
git commit -m "Update pricing copy"
git push origin main
```

---

## 8.2 Feature Branch Workflow

The feature branch workflow has contributors create a new branch for each piece of work — a feature, a fix, an experiment — rather than committing directly to the main branch. Work happens in isolation on that branch, and once it's complete and reviewed, it's merged back into the main branch, typically through a pull request (Lesson 9). This keeps the main branch stable and deployable at all times, since incomplete work never lives there directly, and it's the most common workflow used on real-world software teams today.

```bash
git switch -c feature/checkout-redesign
# ... commit work over several days ...
git push -u origin feature/checkout-redesign
# open a pull request from feature/checkout-redesign into main
```

---

## 8.3 Forking Workflow

The forking workflow is common in open-source projects, where most contributors don't have direct push access to the original repository at all. Instead, a contributor creates their own personal copy of the repository (a "fork") under their own account, makes changes there, and then proposes those changes back to the original project through a pull request. This lets a project's maintainers accept contributions from anyone in the world without needing to grant every contributor write access to the canonical repository, since all incoming changes are explicitly reviewed and merged by a maintainer.

```bash
# after forking "upstream/project" to "yourname/project" on the hosting platform:
git clone https://github.com/yourname/project.git
cd project
git remote add upstream https://github.com/upstream/project.git
git switch -c fix/typo-in-docs
# ... commit changes, push to your fork, then open a PR from your fork to upstream ...
```

---

## 8.4 Trunk-Based Development

Trunk-based development has all contributors integrate very small, frequent changes directly into a single shared branch (the "trunk," usually `main`), rather than working on long-lived feature branches that diverge significantly before merging. Any branches that do exist are extremely short-lived, often merged within a day. This approach minimizes the pain of merge conflicts and integration problems, which tend to grow with how long branches stay separated, but it requires strong automated testing and often feature flags (mechanisms to hide unfinished functionality in production) to keep the constantly-changing trunk stable.

```python
# a feature flag lets unfinished work sit safely on main
if feature_flags.is_enabled("new_checkout_flow"):
    render_new_checkout()
else:
    render_old_checkout()
```

[Previous](./[7]-Remote-Repositories.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[9]-Pull-Requests-And-Code-Review.md)
