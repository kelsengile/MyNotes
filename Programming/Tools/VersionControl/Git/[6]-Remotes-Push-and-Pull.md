[Previous](./[5]-Merging-And-Conflicts.md) | [Table of Contents](./[0]-Introduction-to-Git.md) | [Next](./[7]-Undoing-And-Rewriting-History.md)

# Lesson 6 - Working With Remotes, Pushing And Pulling

## 6.1 What Is a Remote?

A remote is a version of your repository hosted elsewhere — typically on a service like GitHub, GitLab, or Bitbucket, or on a shared server. `origin` is the conventional name for the remote you cloned from.

---

## 6.2 Viewing Remotes

```bash
git remote            # list remote names
git remote -v          # list with URLs (fetch/push)
git remote show origin # detailed info about one remote
```

---

## 6.3 Adding and Removing Remotes

```bash
git remote add origin https://github.com/user/repo.git
git remote add upstream https://github.com/original-owner/repo.git
git remote remove upstream
git remote rename origin old-origin
```

### Changing a Remote's URL

```bash
git remote set-url origin git@github.com:user/repo.git
```

---

## 6.4 Pushing

`git push` uploads your local commits to a remote.

```bash
git push origin main
```

### First Push of a New Branch

```bash
git push -u origin feature-branch
```
The `-u` (`--set-upstream`) flag links your local branch to the remote branch, so future pushes/pulls just need:
```bash
git push
git pull
```

### Force Pushing (Use with Caution)

Overwrites the remote history with your local history. Dangerous on shared branches.

```bash
git push --force
git push --force-with-lease   # safer: fails if remote has commits you haven't seen
```

### Pushing Tags

```bash
git push origin v1.0.0
git push origin --tags
```

### Deleting a Remote Branch

```bash
git push origin --delete feature-branch
```

---

## 6.5 Fetching

`git fetch` downloads new commits/branches from the remote but does **not** merge them into your working branch.

```bash
git fetch origin
git fetch --all
```

After fetching, you can inspect what changed:
```bash
git log main..origin/main --oneline
```

---

## 6.6 Pulling

`git pull` is essentially `git fetch` + `git merge` (or `git rebase`, depending on config).

```bash
git pull origin main
```

### Pull with Rebase (Cleaner History)

```bash
git pull --rebase
```

Set this as the default behavior:
```bash
git config --global pull.rebase true
```

---

## 6.7 Tracking Branches

A local branch can "track" a remote branch, meaning Git knows which remote branch to push to / pull from by default.

```bash
git branch -vv                              # see tracking relationships
git branch --set-upstream-to=origin/main main
```

---

## 6.8 Cloning vs. Adding a Remote

- `git clone <url>` — creates a new local repo, automatically sets up `origin`.
- `git remote add <name> <url>` — adds a remote to an *existing* local repo (e.g. when you `git init`'d locally first).

---

## 6.9 Working with Forks

A common open-source pattern:

```bash
git clone https://github.com/your-username/repo.git
cd repo
git remote add upstream https://github.com/original-owner/repo.git
git fetch upstream
git merge upstream/main
```

---

## 6.10 Summary

- Remotes are named references to other copies of the repo (`origin` by convention).
- `git fetch` downloads without merging; `git pull` fetches and merges (or rebases).
- `git push -u` sets up tracking so future push/pull need no arguments.
- Use `--force-with-lease` instead of `--force` to avoid clobbering others' work.

---

[Previous](./[5]-Merging-And-Conflicts.md) | [Table of Contents](./[0]-Introduction-to-Git.md) | [Next](./[7]-Undoing-And-Rewriting-History.md)
