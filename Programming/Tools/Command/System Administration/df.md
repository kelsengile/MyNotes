[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# df (disk free)

`df` reports how much disk space is used and available on mounted filesystems.

Download: [https://man7.org/linux/man-pages/man1/df.1.html](https://man7.org/linux/man-pages/man1/df.1.html)

---

## What Is df?

`df` reports at the **filesystem** level — it answers "how full is this entire disk/partition?" rather than "how big is this specific folder?" (which is `du`'s job). It reads filesystem-level metadata directly, so it's fast even on enormous disks, since it doesn't need to walk every file the way `du` does.

---

## Core Commands

```bash
df                    # Show usage for all mounted filesystems
df -h                  # Human-readable sizes (K, M, G instead of raw blocks)
df -T                  # Show the filesystem type alongside usage
df /home                # Show usage for the filesystem containing a specific path
df -i                   # Show inode usage instead of block usage
```

---

## All Major Options

| Flag | Meaning |
|---|---|
| `-h` | Human-readable sizes (K/M/G/T) |
| `-H` | Human-readable using powers of 1000 instead of 1024 |
| `-T` | Show filesystem type (ext4, xfs, ntfs, etc.) |
| `-i` | Show inode usage instead of disk block usage |
| `-a` | Include pseudo-filesystems normally hidden (like `/proc`) |
| `-x type` | Exclude a specific filesystem type from the output |

---

## Reading the Output

```
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        50G   32G   16G  67% /
/dev/sda2       100G   45G   50G  48% /home
```

`Use%` is usually the number people care about most — a filesystem consistently near 100% is at risk of running out of space, which can cause anything from failed writes to a completely unresponsive system depending on which filesystem fills up.

---

## Checking Inode Usage

```bash
df -i
```

Disk space and inode count (the number of file "slots" a filesystem can hold) are tracked separately — it's possible for `df -h` to show plenty of free space while `df -i` shows 100% inode usage (common on filesystems with huge numbers of tiny files, like mail queues or caches), which still causes "no space left on device" errors even though raw disk space remains.

---

## Checking a Specific Path's Filesystem

```bash
df -h /var/log
```

Passing a path (rather than no argument, which shows everything) restricts the output to just the filesystem that path lives on — useful when a server has several mounted disks and you only care about the one a particular application writes to.

---

## Common Gotchas

- Confusing df with du: `df` reports the whole filesystem's usage, while `du` reports a specific folder's total size — a folder can appear small under `du` even on a filesystem shown as nearly full under `df`, if other unrelated data on the same disk is consuming the rest.
- Deleted-but-open files: a file that's been deleted but is still held open by a running process continues to consume space that `df` reports as used but that `du` (which only sees files that still exist in the directory tree) can't account for — this can make `df` and manual folder-size totals appear to disagree.

---

## Example Walkthrough

```bash
df -h
df -i /var
```

Checks overall disk space usage across all mounted filesystems, then specifically checks inode usage for `/var` — a common pair of checks when diagnosing a "disk full" error that doesn't match what a folder listing seems to show.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
EOF

cat > "/home/claude/work/System Administration/du.md" << 'EOF'
[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# du (disk usage)

`du` reports how much disk space specific files and directories are using, letting you find exactly what's consuming space on a filesystem.

Download: [https://man7.org/linux/man-pages/man1/du.1.html](https://man7.org/linux/man-pages/man1/du.1.html)

---

## What Is du?

Where `df` reports the total usage of an entire filesystem, `du` walks a directory tree and sums up the size of every file inside it, giving a breakdown at the folder or file level. This makes `du` the right tool for answering "what's taking up all this space?" once `df` has told you a disk is nearly full.

---

## Core Commands

```bash
du -h file.txt                # Human-readable size of a single file
du -sh folder/                 # Summary (total) size of a folder, human-readable
du -h folder/                  # Size of every file and subfolder inside, recursively
du -sh */                      # Total size of each top-level folder in the current directory
du -a folder/                  # Include individual files, not just directories
```

---

## All Major Options

| Flag | Meaning |
|---|---|
| `-h` | Human-readable sizes (K/M/G) |
| `-s` | Summary only — total for each argument, not every subfolder |
| `-a` | Include files as well as directories in the output |
| `-c` | Print a grand total at the end |
| `--max-depth=N` | Limit how many levels deep to report |
| `-x` | Stay on one filesystem, don't cross into mounted filesystems |

---

## Finding What's Eating Disk Space

```bash
du -sh */ | sort -rh | head -10
```

This is one of the most useful one-liners in everyday sysadmin work: get the total size of every top-level folder, sort by size descending (`-r` reverse, `-h` human-readable-aware sort), and show the top 10 biggest offenders — a fast way to find what's consuming space without manually checking each folder.

---

## Limiting Depth for a Manageable Overview

```bash
du -h --max-depth=2 /var
```

Running `du -h` on a huge directory without a depth limit prints a line for every single subdirectory recursively, which is often overwhelming. `--max-depth` caps how deep the breakdown goes, giving a readable high-level view first, before drilling into whichever subfolder actually needs closer inspection.

---

## Excluding Files or Following Symlinks

```bash
du -sh --exclude="*.log" folder/
du -sh -L folder/          # Follow symlinks instead of counting the link itself
```

`--exclude` is useful for getting a folder's "real" size without counting large log files or caches that might be regenerated; `-L` changes how `du` treats symbolic links, since by default it counts a symlink's own (tiny) size rather than following it to the target.

---

## Common Gotchas

- Apparent vs actual size: `du` reports actual disk block usage, which can differ from a file's "apparent size" shown by `ls -l`, especially for sparse files or filesystems with unusual block sizes — `du --apparent-size` reports the size as if fully allocated, matching what `ls` shows.
- Slow on huge trees: `du` has to read metadata for every file in a directory tree, which can be slow on network filesystems or directories with millions of files — `--max-depth` and targeting a specific subfolder both help.

---

## Example Walkthrough

```bash
du -sh /var/log/* | sort -rh | head -5
```

Finds the five largest items inside `/var/log`, sorted from biggest to smallest — a typical first step when a server's disk is filling up and logs are a likely suspect.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)