[Previous](./[13]-Reflog.md) | [Table of Contents](./[0]-Introduction-to-Git.md) | [Next](./[15]-Hooks.md)

# Lesson 14 - Submodules

## 14.1 What Is a Submodule?

A submodule lets you embed one Git repository inside another as a subdirectory, while keeping its own independent history and commits. Common use case: a project depends on a shared library that's itself a separate Git repo.

The parent repo doesn't store the submodule's files directly — it stores a pointer to a specific commit in the submodule's repo.

---

## 14.2 Adding a Submodule

```bash
git submodule add https://github.com/user/library.git libs/library
```

This:
1. Clones the submodule repo into `libs/library`.
2. Adds a `.gitmodules` file recording the URL and path.
3. Stages the submodule as a special entry (a "gitlink") pointing to its current commit.

```bash
git commit -m "Add library as a submodule"
```

### `.gitmodules` File

```ini
[submodule "libs/library"]
	path = libs/library
	url = https://github.com/user/library.git
```

---

## 14.3 Cloning a Repo That Has Submodules

Submodules are **not** cloned automatically by default.

```bash
git clone https://github.com/user/main-project.git
cd main-project
git submodule init
git submodule update
```

Or do it all in one step:
```bash
git clone --recurse-submodules https://github.com/user/main-project.git
```

If you already cloned without submodules:
```bash
git submodule update --init --recursive
```

---

## 14.4 Updating a Submodule

To pull the latest changes from the submodule's own remote:

```bash
cd libs/library
git pull origin main
cd ../..
git add libs/library
git commit -m "Update library submodule to latest"
```

Or update all submodules to the commit currently recorded in `.gitmodules`/the parent repo:
```bash
git submodule update --remote
```

---

## 14.5 Checking Submodule Status

```bash
git submodule status
```
```
 a1b2c3d libs/library (heads/main)
```
A leading `-` means the submodule isn't initialized; a leading `+` means the checked-out commit differs from what's recorded in the parent repo.

---

## 14.6 Working Inside a Submodule

A submodule is a full, independent Git repo — you can `cd` into it and run normal Git commands (`branch`, `commit`, `push`, etc.) as if it were standalone.

```bash
cd libs/library
git switch -c my-fix
# ... make changes, commit ...
git push origin my-fix
cd ../..
git add libs/library     # record the new commit pointer in the parent repo
git commit -m "Point to library's my-fix branch changes"
```

---

## 14.7 Removing a Submodule

```bash
git submodule deinit -f libs/library
git rm -f libs/library
rm -rf .git/modules/libs/library
git commit -m "Remove library submodule"
```

---

## 14.8 Submodules vs. Alternatives

Submodules are notoriously tricky for teams — common alternatives include:

- **Git subtree** — merges another repo's history into a subdirectory, no separate `.gitmodules` bookkeeping, but history becomes intertwined.
- **Package managers** (npm, pip, Cargo, etc.) — often a better fit when the dependency is versioned and published, rather than needing direct source access.
- **Monorepos** — avoiding the split entirely by keeping everything in one repository.

---

## 14.9 Common Pitfalls

- Forgetting `--recurse-submodules` when cloning leaves submodule folders empty.
- Forgetting to `git add` the submodule path after updating it inside the submodule — the parent repo won't know about the new commit.
- Detached HEAD is the default state inside a submodule after `update` — create/checkout a branch before committing new work there.

---

## 14.10 Summary

- Submodules embed a separate Git repo at a specific commit inside your project.
- Cloning requires `--recurse-submodules` or a separate `submodule update --init`.
- The parent repo only tracks *which commit* the submodule is at — updating that pointer requires an explicit `add` + `commit` in the parent repo.

---

[Previous](./[13]-Reflog.md) | [Table of Contents](./[0]-Introduction-to-Git.md) | [Next](./[15]-Hooks.md)
