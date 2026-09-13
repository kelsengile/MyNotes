[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# du (disk usage)

`du` reports how much disk space files and directories are actually consuming. It's the go-to tool for answering "what's eating all my disk space?" — a question `ls -l`, which only shows a file's logical size, often can't answer well.

Reference: [https://man7.org/linux/man-pages/man1/du.1.html](https://man7.org/linux/man-pages/man1/du.1.html)

---

## What Is du?

`du` walks a directory tree and sums the space each file consumes **on disk**, in blocks — which is not always the same as the file's apparent byte size shown by `ls -l`. Filesystems allocate space in fixed-size blocks, so a 1-byte file might still occupy a full 4 KB block on disk; conversely, sparse files can report a large logical size while occupying very little real disk space. `du` reflects the real allocation, which is why its numbers and a file manager's "size" column sometimes disagree.

By default, `du` reports a running total for every directory in the tree, which can be overwhelming — most day-to-day use narrows this down with `-s` (summary) or `-h` (human-readable) and often both together.

---

## Core Commands

```bash
du folder/                     # Recursively list the size of every subdirectory
du -h folder/                  # Same, but in human-readable units (K, M, G)
du -s folder/                  # Just the total size of the folder, no subdirectory breakdown
du -sh folder/                 # Combine: one human-readable total
du -sh *                       # Total size of every item in the current directory
du -a folder/                  # Include individual files, not just directories, in the listing
du -d 1 folder/                # Limit depth: only show one level of subdirectories
du -c file1 file2               # Print a grand total after listing individual sizes
du -x /                        # Stay on one filesystem; don't descend into other mounted filesystems
```

## Full Option Reference

| Flag | Long form | Meaning |
|---|---|---|
| `-h` | `--human-readable` | Sizes in K/M/G/T instead of raw block counts |
| `-s` | `--summarize` | Show only a total for each argument, not every subdirectory |
| `-a` | `--all` | Include files as well as directories |
| `-c` | `--total` | Print a grand total at the end |
| `-d N` | `--max-depth=N` | Only descend N levels before summarizing |
| `-x` | `--one-file-system` | Don't cross into other mounted filesystems |
| `--apparent-size` | | Report logical file size instead of actual disk block usage |
| `-t SIZE` | `--threshold=SIZE` | Only report entries at least (or at most, with `-t -SIZE`) this size |
| `--exclude=PATTERN` | | Skip files matching a glob pattern |

---

## Finding the Biggest Space Hogs

A classic combo for hunting down what's filling up a disk:

```bash
du -sh */ | sort -rh | head -n 10
```

This computes the total size of every top-level subdirectory, sorts them largest-first (`sort -rh` understands human-readable suffixes like "1.2G"), and shows the top 10 — a quick way to find where disk space is disappearing before diving deeper into a specific offending folder.

---

## du vs. df: A Common Point of Confusion

- **`du`** measures space used by *files you point it at* — it walks a directory tree and sums up.
- **`df`** measures space used/free on entire *filesystems/mount points* — it reads filesystem-level metadata, not individual files.

They can disagree: `df` might show a filesystem as nearly full while `du` on the visible directory tree doesn't add up to that total. Common causes include deleted-but-still-open files (a process still holding a file descriptor to a file that's been unlinked keeps its space "used" until the process exits or closes it), or a filesystem mounted at that point with additional hidden data (like a `lost+found` directory or filesystem journal).

---

## Other Practical Uses

- **Auditing before backups** — checking `du -sh` on candidate directories to estimate backup size or transfer time.
- **Docker/container image bloat** — `du -sh` inside a container or on layer directories to find what's making an image large.
- **Quota enforcement scripts** — periodic `du` runs feeding alerts when a user's or project's directory crosses a threshold.
- **Combining with `find`** for size-based cleanup: `find . -type f -size +100M` for a `du`-adjacent view centered on individual large files rather than directory totals.

---

## Related Tools

- `df -h` — filesystem-level free/used space.
- `ncdu` — an interactive, navigable terminal UI built on the same idea as `du`, much faster to explore visually.
- `find -size` — locate individual files above/below a size threshold.
- `stat` — inspect a single file's exact size and block allocation.

---

## Example Walkthrough

```bash
du -sh /var/log/*
du -sh /var/log/* | sort -rh | head -5
```

Lists the size of everything inside `/var/log`, then narrows that down to the five largest items — a typical first step when a server alerts that its disk is nearly full and logs are the usual suspect.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)