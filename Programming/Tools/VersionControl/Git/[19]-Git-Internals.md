[Previous](./[18]-Pull-Requests.md) | [Table of Contents](./[0]-Introduction-to-Git.md)

# Lesson 19 - Git Internals - Objects, Refs, And The .git Directory

## 19.1 The `.git` Directory

Every Git repository's entire history and metadata lives inside `.git/`:

```bash
ls .git/
```
```
HEAD
config
description
hooks/
info/
objects/
refs/
index
```

| Item | Purpose |
|---|---|
| `HEAD` | Pointer to the current branch (or commit, if detached) |
| `config` | Repo-local configuration |
| `objects/` | The actual database of all content: blobs, trees, commits |
| `refs/` | Pointers to commits — branches and tags |
| `index` | The staging area |
| `hooks/` | Scripts triggered at various Git events |

---

## 19.2 Git's Object Model

Git is fundamentally a content-addressable filesystem: everything is stored as an object, identified by the SHA-1 (or SHA-256, in newer repos) hash of its content.

### Four Object Types

1. **Blob** — the raw content of a file (no filename, no metadata — just content).
2. **Tree** — like a directory listing: maps filenames to blob (or sub-tree) hashes.
3. **Commit** — points to one tree (the project snapshot), one or more parent commits, plus author/committer/message metadata.
4. **Tag** — (annotated tags only) points to a commit, with tagger name, date, and message.

```
Commit
 ├── tree (snapshot of the whole project at this point)
 │    ├── blob (content of file1.txt)
 │    ├── blob (content of file2.txt)
 │    └── tree (subdirectory)
 │         └── blob (content of file3.txt)
 └── parent commit(s)
```

### Inspecting Objects Directly

```bash
git cat-file -t a1b2c3d      # show object type: blob, tree, commit, or tag
git cat-file -p a1b2c3d      # pretty-print object content
```

Example — inspecting a commit:
```bash
git cat-file -p HEAD
```
```
tree 8f3a2b1...
parent d4e5f6g...
author Jane Doe <jane@example.com> 1721654400 +0800
committer Jane Doe <jane@example.com> 1721654400 +0800

Fix pagination bug
```

Example — inspecting the tree it points to:
```bash
git cat-file -p 8f3a2b1
```
```
100644 blob 3b2c1a9...    index.html
100644 blob 9d8e7f6...    style.css
040000 tree 1a2b3c4...    scripts
```

---

## 19.3 How a Commit Is Actually Created

When you run `git commit`:
1. Git creates a **blob** for each changed file's content (if it doesn't already exist).
2. Git creates **tree** objects representing the directory structure, referencing those blobs.
3. Git creates a **commit** object pointing to the root tree, plus the parent commit(s) and metadata.
4. The current branch ref (e.g. `refs/heads/main`) is updated to point to this new commit.

Because objects are addressed by content hash, **identical content is only ever stored once** — this is why Git is so efficient even with many similar file versions across history.

---

## 19.4 Refs

A "ref" is simply a human-readable name pointing to a commit hash.

```bash
cat .git/refs/heads/main
```
```
a1b2c3d4e5f6...
```

- `refs/heads/<branch>` — local branches
- `refs/remotes/<remote>/<branch>` — remote-tracking branches
- `refs/tags/<tag>` — tags

### `HEAD`

`HEAD` usually contains a *symbolic reference* to the current branch:
```bash
cat .git/HEAD
```
```
ref: refs/heads/main
```
In detached HEAD state, it instead contains a raw commit hash directly.

---

## 19.5 Packfiles

Loose objects (one file per object in `.git/objects/`) are periodically compressed into **packfiles** by `git gc`, which deduplicates and delta-compresses similar objects for space efficiency. This is why cloned/fetched repos transfer as compact `.pack` files rather than thousands of individual object files.

```bash
git gc
ls .git/objects/pack/
```

---

## 19.6 The Index (Staging Area)

`.git/index` is a binary file listing every staged file, along with its mode, hash, and metadata — effectively a snapshot of what the *next* commit's tree will look like.

```bash
git ls-files --stage
```
```
100644 3b2c1a9... 0    index.html
100644 9d8e7f6... 0    style.css
```

---

## 19.7 Low-Level ("Plumbing") vs. High-Level ("Porcelain") Commands

Commands like `commit`, `branch`, `merge`, `log` are **porcelain** — user-facing, friendly. Underneath, they call **plumbing** commands that manipulate objects and refs directly:

```bash
git hash-object -w file.txt          # create a blob, get its hash
git update-index --add file.txt       # manually add to the index
git write-tree                         # write the index out as a tree object
git commit-tree <tree> -m "message"    # create a commit object
git update-ref refs/heads/main <hash>  # point a branch at a commit
```

Understanding these makes it much easier to reason about what higher-level commands like `reset`, `rebase`, and `merge` are actually doing under the hood.

---

## 19.8 Why This Matters

Understanding internals demystifies commands that otherwise feel like magic:
- `git reset --hard` just moves a ref and updates the working directory/index to match that commit's tree.
- `git branch` just writes a new file under `refs/heads/`.
- A "detached HEAD" is simply `HEAD` holding a raw hash instead of a symbolic ref.
- Rebasing works by replaying commits — creating brand-new commit objects with new parents (hence new hashes) — one at a time.

---

## 19.9 Summary

- Git stores everything as content-addressed objects: blobs (file content), trees (directory structure), commits (snapshots + history), and tags.
- Refs are just named pointers to commits, stored as simple files under `refs/`.
- `HEAD` points to the current branch (or a raw commit hash, when detached).
- Packfiles compress loose objects for efficient storage and transfer.
- Nearly every high-level Git command is ultimately just object creation plus ref updates.

*This concludes the Git lessons series.*

---

[Previous](./[18]-Pull-Requests.md) | [Table of Contents](./[0]-Introduction-to-Git.md)
