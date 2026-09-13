[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# mv (move/rename)

`mv` moves files and directories from one location to another — and, because "moving" a file within the same folder just changes its name, `mv` is also the standard way to rename files and directories on Unix-like systems.

Reference: [https://man7.org/linux/man-pages/man1/mv.1.html](https://man7.org/linux/man-pages/man1/mv.1.html)

---

## What Is mv?

Unlike `cp`, `mv` doesn't necessarily duplicate any data. If the source and destination are on the **same filesystem**, `mv` simply updates the directory entry (the "name → inode" mapping) — an operation that's essentially instantaneous no matter how large the file is, because no file content is read or rewritten. If the source and destination are on **different filesystems** (e.g., moving from your internal drive to a USB stick), `mv` has to fall back to copying the data and then deleting the original, since inodes are only meaningful within a single filesystem.

This distinction explains a lot of `mv`'s real-world behavior: moving a 50 GB file to a folder on the same disk is instant, while moving it to an external drive takes as long as copying it would.

---

## Core Commands

```bash
mv old_name.txt new_name.txt        # Rename a file
mv file.txt /path/to/folder/        # Move a file into a folder, keeping its name
mv folder/ /path/to/new_folder/     # Move (or rename) an entire directory — no -r needed
mv -i file.txt existing.txt         # Prompt before overwriting an existing file
mv -n file.txt existing.txt         # Never overwrite an existing destination file
mv -v file.txt backup/              # Print a confirmation as the move happens
mv -u file.txt backup/              # Only move if source is newer than destination
mv file1 file2 file3 dest/          # Move multiple files into a directory
```

## Full Option Reference

| Flag | Long form | Meaning |
|---|---|---|
| `-i` | `--interactive` | Prompt before overwrite |
| `-f` | `--force` | Overwrite without prompting, even over read-only destination files |
| `-n` | `--no-clobber` | Never overwrite an existing file |
| `-u` | `--update` | Move only when source is newer than destination or destination is missing |
| `-v` | `--verbose` | Print what's happening |
| `-b` | `--backup[=CONTROL]` | Make a backup of each existing destination file before overwriting |
| `-t DIR` | `--target-directory=DIR` | Specify the destination directory explicitly (useful with `xargs`) |
| `-T` | `--no-target-directory` | Treat the destination as a normal file, not a directory (avoids surprises when destination happens to be a directory) |

Notably, `mv` has no `-r`/`--recursive` flag — moving a directory doesn't need one, because (on the same filesystem) it's just a rename of the top-level directory entry; everything underneath moves with it automatically.

---

## Renaming Is Just a Special Case of Moving

```bash
mv report.txt report_final.txt
```

There's no separate "rename" command on Unix-like systems — this single tool handles both jobs because, at the filesystem level, they're the same operation: updating a name-to-inode mapping.

---

## Batch Renaming Patterns

`mv` itself only takes one destination, so bulk renames typically combine it with a loop or another tool:

```bash
for f in *.jpeg; do mv "$f" "${f%.jpeg}.jpg"; done   # Rename all .jpeg to .jpg
```

For more complex batch renames, dedicated tools like `rename` (Perl-based, common on Debian/Ubuntu) or `mmv` are often used instead of scripting around `mv`.

---

## Common Pitfalls

- **Cross-filesystem moves are copy + delete**, so a power loss or interrupted move partway through a large cross-device transfer can leave a partial file at the destination and the original still (partially) intact — unlike a same-filesystem move, which is atomic.
- **Moving into a nonexistent directory** silently renames to that path instead of erroring in some shells' tab-completion scenarios — always double-check the destination exists if you intend to move *into* a folder.
- **No trash/undo** — `mv` (like `rm`) doesn't ask "are you sure" by default when overwriting, and there's no built-in undo. Many admins alias `mv` to `mv -i`.
- **`-T` is easy to forget** when scripting: `mv src dst` treats an existing `dst` directory as a destination folder (moving `src` inside it) rather than renaming `src` to exactly `dst`; `-T` forces the literal-rename interpretation.

---

## Related Tools

- `cp` — duplicates instead of relocating.
- `rename` / `mmv` — pattern-based batch renaming tools built on top of the same underlying rename syscall.
- `rsync -a --remove-source-files` — a move-like operation across systems/network with resumability, useful where a plain cross-device `mv` would be fragile.

---

## Example Walkthrough

```bash
mv draft.md article.md
mv article.md ~/Documents/blog/
mv ~/Downloads/photos/ ~/Pictures/2026-vacation/
```

Renames a draft file, moves the renamed file into a documents folder, and relocates an entire photo directory (and everything inside it) into a new home — all without duplicating a single byte, since each move happens on the same filesystem.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)