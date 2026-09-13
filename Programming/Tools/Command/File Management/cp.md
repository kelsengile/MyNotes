[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# cp (copy)

`cp` copies files and directories from one location to another, leaving the original in place. It's the command-line equivalent of copy-paste, and it's one of the oldest utilities in Unix — it has existed, largely unchanged in spirit, since Version 1 AT&T Unix in the early 1970s.

Reference: [https://man7.org/linux/man-pages/man1/cp.1.html](https://man7.org/linux/man-pages/man1/cp.1.html)

---

## What Is cp?

`cp` is part of GNU Coreutils on Linux (and a BSD-licensed equivalent ships with macOS and the BSDs). Its job sounds trivial — duplicate bytes from A to B — but it actually has to make a lot of decisions on your behalf: whether to follow symbolic links, whether to preserve permissions and timestamps, whether to overwrite existing files, how to treat special files (device nodes, sockets, named pipes), and whether the destination is a file, a directory, or doesn't exist yet.

By default `cp` only copies individual files — copying an entire folder requires the `-r` (recursive) flag, since folders can contain their own subfolders and files that need to be copied along with them. This is a deliberate safety design: `cp` refuses to silently do something as big as duplicating an entire directory tree unless you explicitly ask for it.

### How cp Decides the Destination

- `cp source dest` → if `dest` doesn't exist, it becomes a copy of `source` with that new name.
- `cp source dest` → if `dest` is an existing directory, the file is copied *into* that directory, keeping its original name.
- `cp file1 file2 file3 dest/` → with multiple sources, the last argument **must** be a directory.

---

## Core Commands

```bash
cp file.txt backup.txt              # Copy a file, giving the copy a new name
cp file.txt /path/to/folder/        # Copy a file into a folder, keeping its name
cp -r folder/ backup_folder/        # Recursively copy an entire folder
cp -i file.txt existing.txt         # Prompt before overwriting an existing file
cp -v file.txt backup.txt           # Print a confirmation as the copy happens
cp -u src.txt dst.txt               # Only copy if src is newer than dst, or dst is missing
cp -n src.txt dst.txt               # Never overwrite an existing destination file
cp -p file.txt backup.txt           # Preserve mode, ownership, and timestamps
cp -a folder/ backup/               # "Archive" mode: recursive + preserve everything + no dereference
cp -l file.txt link.txt             # Create a hard link instead of copying data
cp -s file.txt link.txt             # Create a symbolic link instead of copying data
cp --parents a/b/c.txt dest/        # Recreate the source's directory structure under dest
```

## Full Option Reference

| Flag | Long form | Meaning |
|---|---|---|
| `-r`, `-R` | `--recursive` | Copy directories and their contents |
| `-i` | `--interactive` | Ask before overwriting |
| `-f` | `--force` | Remove an existing destination that can't be opened, then retry |
| `-n` | `--no-clobber` | Never overwrite an existing file |
| `-u` | `--update` | Copy only when the source is newer |
| `-v` | `--verbose` | Explain what's being done |
| `-p` | `--preserve` | Preserve mode, ownership, timestamps |
| `-a` | `--archive` | Same as `-dR --preserve=all` — the standard flag for backups |
| `-l` | `--link` | Hard-link files instead of copying |
| `-s` | `--symbolic-link` | Symlink files instead of copying |
| `-d` | | Preserve links and don't dereference symlinks |
| `--reflink` | | Use copy-on-write cloning on filesystems that support it (Btrfs, XFS, APFS) — near-instant "copies" that only diverge on write |
| `-x` | `--one-file-system` | Stay on the source filesystem; don't cross mount points |

---

## The -r Flag Is Not Optional for Folders

```bash
cp folder/ backup/
# cp: -r not specified; omitting directory 'folder/'

cp -r folder/ backup/
# Works: copies the folder and everything inside it
```

Forgetting `-r` when copying a directory is one of the most common `cp` mistakes — the command silently skips the folder rather than copying it, which can be confusing in scripts that don't check exit codes.

---

## Beyond Plain Copying: Other Things cp Can Do

- **Copy-on-write cloning (`--reflink=auto`)**: on modern filesystems like Btrfs or XFS with reflink support, `cp` can create a copy that shares disk blocks with the original until one of the two is modified. This makes copying huge files (VM images, datasets) nearly instantaneous and saves disk space.
- **Preserving extended attributes and SELinux context** with `--preserve=context,xattr` — important when copying files that carry security labels or custom metadata.
- **Sparse file handling** (`--sparse=always|auto|never`): `cp` can detect "holes" in sparse files (like disk images) and avoid writing actual zero blocks for them, keeping the copy small.
- **Backing up the destination before overwriting** with `-b` or `--backup[=CONTROL]`, which appends a suffix like `~` to the old version instead of destroying it outright.
- **Building hard-link or symlink trees** with `-l`/`-s`, useful for tools like `rsync --link-dest` style backup snapshots where you want many "copies" that don't consume extra disk space until content changes.
- **Interacting with sockets, FIFOs, and device files**: on many systems `cp` can duplicate special files rather than reading their contents, depending on flags — see `--preserve=links` behavior.

---

## Common Pitfalls

- **Trailing slashes matter for directories.** `cp -r src dst/` behaves differently than `cp -r src/ dst/` on some systems when `dst` doesn't already exist — check documentation or test carefully.
- **Overwriting without warning.** Plain `cp` overwrites destination files silently unless `-i` or `-n` is used. Many admins alias `cp` to `cp -i` for safety.
- **Copying symlinks vs. their targets.** By default `cp` follows symlinks and copies the file they point to; use `-P`/`--no-dereference` (implied by `-a`) to copy the link itself.
- **Permissions aren't preserved by default** — a fresh `cp` gets new timestamps and inherits the umask for permissions unless `-p` or `-a` is used.

---

## Related Tools

- `mv` — moves/renames instead of copying (no duplication of data on the same filesystem).
- `rsync` — a much more capable copy tool for large trees, remote transfers, and incremental syncs.
- `scp` / `sftp` — copy files over SSH to remote machines.
- `dd` — low-level, block-by-block copying, often used for disk images.
- `tar` — bundles a tree into a single stream, often piped to copy across systems (`tar cf - . | (cd dest && tar xf -)`).

---

## Example Walkthrough

```bash
cp -r project/ project_backup/
cp -p project/config.yaml project/config.yaml.bak
cp -a important_data/ /mnt/backup_drive/important_data/
```

Makes a full backup copy of an entire project folder, separately backs up just one important config file with its original timestamps preserved, and then makes an archive-quality copy of a data folder onto a backup drive — preserving permissions, ownership, timestamps, and symlinks exactly as they were.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)