[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# mkdir (make directory)

`mkdir` creates new, empty directories (folders). It's one of the very first commands most people learn, but it carries a surprising amount of depth around permissions, parent-directory creation, and how directories actually work under the hood.

Reference: [https://man7.org/linux/man-pages/man1/mkdir.1.html](https://man7.org/linux/man-pages/man1/mkdir.1.html)

---

## What Is mkdir?

On Unix-like systems, a directory is really just a special kind of file: one that stores a table mapping names to inodes (the on-disk structures representing files). `mkdir` allocates a new inode for that table and links it into its parent directory, initialized with the two default entries `.` (the directory itself) and `..` (its parent).

By default, `mkdir` requires every parent directory in the path to already exist — it won't create an entire nested path in one call unless told to.

---

## Core Commands

```bash
mkdir new_folder                    # Create a single directory
mkdir folder1 folder2 folder3       # Create multiple directories at once
mkdir -p a/b/c                      # Create nested directories, making parents as needed
mkdir -v new_folder                 # Print a message confirming creation
mkdir -m 700 private_folder         # Create a directory with specific permissions
```

## Full Option Reference

| Flag | Long form | Meaning |
|---|---|---|
| `-p` | `--parents` | Create parent directories as needed; no error if the directory already exists |
| `-v` | `--verbose` | Print a message for each created directory |
| `-m MODE` | `--mode=MODE` | Set permission bits (like `chmod`) on the new directory instead of the default `umask`-derived mode |
| | `--context=CTX` | Set the SELinux security context |

---

## Without -p: Missing Parents Are an Error

```bash
mkdir projects/2026/reports
# mkdir: cannot create directory 'projects/2026/reports': No such file or directory

mkdir -p projects/2026/reports
# Works: creates projects/, projects/2026/, and projects/2026/reports/ in one step
```

`-p` is also idempotent — running it again on a path that already exists does nothing and doesn't error, which makes it the standard choice inside scripts that need to guarantee a directory exists ("ensure this path is there") without caring whether it was there before.

---

## Permissions on New Directories

Every new directory's permissions are determined by `mode & ~umask`, where the default requested mode is `0777` (`rwxrwxrwx`). If your shell's `umask` is the common `022`, a plain `mkdir` produces `0755` (`rwxr-xr-x`) directories — owner can read/write/enter, others can only read/enter.

```bash
mkdir -m 700 secrets/     # Only the owner can read, write, or enter this directory
```

Note that directory permission bits mean something slightly different from file permission bits: the **execute** bit on a directory controls whether you can `cd` into it or access files inside by name, not whether you can "run" it.

---

## Other Things Worth Knowing

- **`mkdir` vs. `os.makedirs`/`File.mkdir` in programming languages**: most languages' standard libraries expose both a plain "make one directory" call and a "make with parents" call, mirroring the `mkdir` / `mkdir -p` distinction.
- **Race conditions in scripts**: `test -d dir || mkdir dir` has a tiny race window between the check and the creation in concurrent scripts; `mkdir -p` (or handling the "already exists" error) is safer.
- **Temporary directories**: for one-off scratch space, `mktemp -d` is generally preferred over `mkdir` with a hardcoded name, since it guarantees a unique, unpredictable name and avoids collisions.
- **Windows equivalent**: `md` or `mkdir` in Command Prompt, and `New-Item -ItemType Directory` in PowerShell, both of which create intermediate directories by default (unlike Unix `mkdir` without `-p`).

---

## Related Tools

- `rmdir` — removes empty directories (the natural counterpart to `mkdir`).
- `rm -r` — removes directories and their contents recursively.
- `mktemp -d` — creates a uniquely named temporary directory.
- `install -d` — creates directories with specific ownership/permissions in one step, often used in build/install scripts.

---

## Example Walkthrough

```bash
mkdir -p ~/projects/website/{src,dist,tests}
mkdir -m 700 ~/projects/website/secrets
```

Creates a full project skeleton in one command using brace expansion — `src/`, `dist/`, and `tests/` are all created under a freshly made `website/` folder — then adds a locked-down `secrets/` directory that only the owner can access.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)