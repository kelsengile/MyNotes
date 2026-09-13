[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# touch

`touch` creates empty files if they don't exist, and updates a file's timestamps if it does. The name comes from the idea of "touching" a file — the way physically touching a piece of paper doesn't change its contents but does prove you handled it.

Reference: [https://man7.org/linux/man-pages/man1/touch.1.html](https://man7.org/linux/man-pages/man1/touch.1.html)

---

## What Is touch?

Every file on a Unix-like filesystem carries at least three timestamps in its inode:

- **mtime** (modification time) — when the file's *content* last changed
- **atime** (access time) — when the file was last *read*
- **ctime** (change time) — when the file's *metadata* (permissions, owner, etc.) last changed

`touch`'s primary purpose is to update `atime` and `mtime` to the current time (or a time you specify) without altering the file's actual content. As a side effect, if the target file doesn't exist, `touch` creates it as an empty (zero-byte) file — which is why most people know `touch` purely as "the command that makes an empty file."

---

## Core Commands

```bash
touch newfile.txt              # Create an empty file, or update its timestamp if it exists
touch file1.txt file2.txt      # Create/touch multiple files at once
touch -a file.txt              # Update only the access time
touch -m file.txt              # Update only the modification time
touch -t 202601011200 file.txt # Set a specific timestamp: YYYYMMDDhhmm
touch -d "2 hours ago" file.txt  # Set timestamp using a natural-language date
touch -r reference.txt file.txt # Copy the timestamp from another file
touch -c file.txt              # Don't create the file if it doesn't already exist
```

## Full Option Reference

| Flag | Long form | Meaning |
|---|---|---|
| `-a` | | Change only the access time |
| `-m` | | Change only the modification time |
| `-c` | `--no-create` | Do not create the file if it doesn't exist |
| `-t STAMP` | | Use `[[CC]YY]MMDDhhmm[.ss]` as the timestamp |
| `-d STRING` | `--date=STRING` | Parse a human-readable date/time string |
| `-r FILE` | `--reference=FILE` | Use another file's timestamps instead of the current time |
| `-h` | `--no-dereference` | Affect a symlink itself rather than the file it points to |

---

## Why "Just Make an Empty File" Matters

Empty placeholder files created with `touch` show up constantly in real workflows:

- **Build systems** (like `make`) use file timestamps to decide what needs rebuilding — `touch`ing a source file forces it to be considered "newer," triggering a rebuild without editing anything.
- **Testing directory structures** — quickly scaffold a set of expected files: `touch src/{a,b,c}.py`.
- **Signaling / lock files** — some scripts and daemons create an empty file as a simple flag ("this step has completed", "a process is running") that other scripts check for with `test -f`.
- **`.gitkeep` convention** — Git doesn't track empty directories, so developers `touch` a placeholder file inside one to force Git to keep the folder.

---

## Manipulating Time Deliberately

Beyond just "now," `touch` is a genuine date/time-manipulation tool:

```bash
touch -d "next Monday 09:00" reminder.txt
touch -d "1 week ago" old_report.txt
touch -r original.txt copy.txt      # Make copy.txt look exactly as old as original.txt
```

This is useful for testing time-based logic (backup scripts, log rotation, cache expiry) without waiting for real time to pass, or for restoring a file's apparent age after an edit that shouldn't count as "new."

---

## Common Pitfalls

- **`touch` never truncates or clears an existing file's content** — it only changes metadata. People sometimes confuse it with `> file.txt`, which *does* empty an existing file.
- **Access time updates can be disabled at the filesystem level** (`noatime` mount option), in which case `touch -a` may have no visible effect on some systems.
- **Symlinks**: without `-h`, touching a symlink updates the timestamp of the file it points to, not the link itself.

---

## Related Tools

- `stat file.txt` — view a file's full timestamp and metadata details.
- `date` — the general-purpose date/time utility that `touch -d` borrows its parsing from.
- `find . -newer reference.txt` — locate files modified more recently than a reference file, often paired with `touch` for scripting.

---

## Example Walkthrough

```bash
touch src/{main,utils,config}.py
touch -d "yesterday" .last_run
find . -newer .last_run
```

Scaffolds three empty Python source files at once, creates a marker file stamped as "yesterday," then uses `find` to list every file modified more recently than that marker — a common pattern in incremental-processing scripts.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)