[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# rm (remove)

`rm` deletes files and directories. Deletion is immediate and, on most systems, does not go through any recycle bin — this is the single most important thing to understand about `rm`.

Download: [https://man7.org/linux/man-pages/man1/rm.1.html](https://man7.org/linux/man-pages/man1/rm.1.html)

---

## What Is rm?

`rm` unlinks a file's directory entry, and once no references to the underlying data remain, the space is marked free for reuse. There's no built-in undo: the data isn't securely wiped, but it's also not trivially recoverable through normal commands once removed, and can be overwritten by any new file at any time. This makes `rm` one of the few genuinely dangerous everyday commands.

---

## Core Commands

```bash
rm file.txt                 # Delete a file
rm -r folder/                # Recursively delete a directory and its contents
rm -f file.txt                # Force delete, no prompt, no error if missing
rm -rf folder/                # Recursive + force — deletes without any confirmation
rm -i file.txt                 # Prompt for confirmation before deleting
rm -v file.txt                 # Verbose — print each file as it's removed
```

---

## All Major Options

| Flag | Meaning |
|---|---|
| `-r` / `-R` | Recursive — required to delete directories |
| `-f` | Force — never prompt, ignore nonexistent files |
| `-i` | Interactive — prompt before every removal |
| `-I` | Prompt once for large recursive deletions instead of per-file |
| `-v` | Verbose — print what's being deleted |
| `-d` | Remove empty directories (without needing `-r`) |
| `--preserve-root` | Refuse to operate recursively on `/` (default behavior in modern GNU rm) |

---

## Why -rf Is Dangerous

```bash
rm -rf folder/
```

`-r` allows recursing into directories, and `-f` suppresses every confirmation and error — combined, this command deletes an entire directory tree instantly and silently, including anything inside it, with no prompt and no way to recover it afterward through `rm` itself. Mistyping a path (especially with variable expansion in a script, like `rm -rf "$dir/"* ` when `$dir` is accidentally empty) is a classic and often catastrophic mistake.

---

## Safer Habits

```bash
rm -i important_file.txt
alias rm='rm -i'          # A common shell alias to make -i the default
ls folder/ && rm -r folder/  # List before deleting, as a manual sanity check
```

Because `rm` has no trash bin, many people alias `rm` to always prompt, or use a `trash`/`trash-cli` utility instead for everyday deletions, reserving raw `rm -rf` for cases where permanent, unprompted deletion is genuinely intended (like scripted cleanup of temp files).

---

## Removing Empty Directories

```bash
rm -d empty_folder/
rmdir empty_folder/
```

`rmdir` is a separate, narrower command that only removes empty directories and refuses if anything is inside — a much safer choice than `rm -r` when you specifically mean "remove this directory only if it's already empty."

---

## Deleting by Pattern

```bash
find . -name "*.tmp" -delete
rm ./*.tmp
```

`rm` itself doesn't support recursive pattern matching across subdirectories — combining it with `find ... -delete`, or `find ... -exec rm {} \;`, is the standard way to remove files matching a pattern throughout a directory tree.

---

## Common Gotchas

- Wildcard expansion surprises: `rm -rf ./*` behaves very differently from `rm -rf ~*` or an unquoted variable that happens to expand to nothing — always double-check a risky `rm` command, especially inside scripts, before running it.
- No confirmation on symlinks: `rm` on a symlink removes the link itself, not the target it points to — a common point of confusion when a symlinked file "disappears" as expected but the real file elsewhere remains.

---

## Example Walkthrough

```bash
ls old_build/
rm -rf old_build/
```

Lists a directory's contents as a final sanity check before permanently deleting it and everything inside — a small habit that catches a surprising number of near-misses.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
EOF

cat > "/home/claude/work/File Management/touch.md" << 'EOF'
[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# touch

`touch` creates empty files if they don't exist, and updates the modification/access timestamps of files that do.

Download: [https://man7.org/linux/man-pages/man1/touch.1.html](https://man7.org/linux/man-pages/man1/touch.1.html)

---

## What Is touch?

`touch`'s original purpose (its name comes from "touching" a file to update its timestamp) is timestamp management, which matters a lot to build tools like `make` that decide whether to rebuild based on file modification times. Creating a new empty file is really a side effect: if the named file doesn't exist, `touch` creates it (with a fresh timestamp) since there's nothing existing to update.

---

## Core Commands

```bash
touch file.txt                   # Create an empty file, or update its timestamp if it exists
touch file1.txt file2.txt         # Create/update multiple files at once
touch -t 202401011200 file.txt    # Set a specific timestamp (YYYYMMDDhhmm)
touch -d "2 hours ago" file.txt   # Set timestamp using a natural-language date
touch -r reference.txt file.txt    # Copy the timestamp from another file
touch -a file.txt                  # Update only the access time
touch -m file.txt                  # Update only the modification time
```

---

## All Major Options

| Flag | Meaning |
|---|---|
| `-a` | Change only the access time |
| `-m` | Change only the modification time |
| `-t STAMP` | Use a specific timestamp instead of the current time |
| `-d STRING` | Parse a human-readable date/time string |
| `-r FILE` | Use another file's timestamp as the reference |
| `-c` | Don't create the file if it doesn't exist (only update if present) |

---

## Why Timestamps Matter to Build Tools

```bash
touch main.c
make
```

Tools like `make` decide whether to rebuild a target by comparing modification timestamps of source files against build outputs. Deliberately `touch`-ing a source file (without changing its content) is a common trick to force a rebuild of everything that depends on it, without needing to make an actual edit.

---

## Creating Placeholder Files

```bash
touch .gitkeep
touch README.md CHANGELOG.md LICENSE
```

Git doesn't track empty directories, so a common convention is to `touch` a placeholder file like `.gitkeep` inside an otherwise-empty folder so it gets committed. `touch` is also a fast way to scaffold several empty files that will be filled in later.

---

## Backdating or Fixing Timestamps

```bash
touch -d "2023-06-15 10:00:00" report.txt
touch -r original.txt copy.txt
```

`-d` accepts many natural-language date formats, and `-r` is handy after a copy or restore operation where you want a file's timestamp to exactly match another file's, rather than reflecting whenever the copy happened.

---

## Common Gotchas

- `-c` vs default: without `-c`, `touch` silently creates any file that doesn't exist, which is usually desired but can be surprising in a script that expected an error for a typo'd filename.
- Timestamp precision: some filesystems store timestamps with limited precision, so very fine-grained `-t` values may be rounded.

---

## Example Walkthrough

```bash
touch -d "yesterday" old_report.txt
ls -l old_report.txt
```

Creates a file and explicitly backdates its modification time to yesterday, then confirms the timestamp with a long listing — useful when testing scripts or tools that behave differently based on file age.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
EOF

cat > "/home/claude/work/File Management/tree.md" << 'EOF'
[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# tree

`tree` displays a directory's contents as an indented, visual tree, making it easy to see nested folder structure at a glance instead of reading a flat file listing.

Download: [https://oldmanprogrammer.net/source.php?dir=projects/tree](https://oldmanprogrammer.net/source.php?dir=projects/tree)

---

## What Is tree?

`ls -R` can list a directory recursively, but its output is a series of flat, disconnected lists per subdirectory. `tree` instead draws the hierarchy explicitly with connecting lines, making parent/child relationships visually obvious — which is why READMEs and documentation often include a `tree`-style diagram to describe a project's layout (as this very repository's own CONTRIBUTING.md does).

---

## Core Commands

```bash
tree                        # Show the tree for the current directory
tree /path/to/folder          # Show the tree for a specific directory
tree -L 2                     # Limit depth to 2 levels
tree -d                       # Show only directories, no files
tree -a                       # Include hidden files (dotfiles)
tree -I "node_modules|.git"   # Ignore specific patterns
tree -f                       # Print full paths instead of just names
```

---

## All Major Options

| Flag | Meaning |
|---|---|
| `-L n` | Limit the depth of the tree to n levels |
| `-d` | List directories only |
| `-a` | Show hidden files too |
| `-I pattern` | Ignore files/folders matching a pattern (pipe-separated for multiple) |
| `-f` | Print the full path prefix for each entry |
| `-h` | Print file sizes in human-readable form |
| `--du` | Show the cumulative size of each directory |
| `-C` | Force colorized output |

---

## Limiting Depth for Large Projects

```bash
tree -L 2
```

On a large project, an unrestricted `tree` can print thousands of lines. `-L` caps how many levels deep it recurses, giving a manageable high-level overview — often exactly what's needed for documentation like a project's README.

---

## Excluding Noise

```bash
tree -I "node_modules|.git|__pycache__|dist"
```

Multiple patterns can be combined with `|` inside a single `-I` argument. This is the standard way to keep generated or dependency folders (which can be enormous) out of a tree diagram meant for humans to read.

---

## File Sizes and Disk Usage

```bash
tree -h --du
```

`--du` annotates each directory with its total size (recursively), and `-h` formats those sizes in human-readable units (K, M, G) — a quick way to visually spot which subfolder is eating the most disk space.

---

## Generating Documentation Trees

```bash
tree -L 3 -I "node_modules" > STRUCTURE.md
```

Redirecting `tree`'s output to a file is a common way to generate the kind of structural diagram often pasted into a README or CONTRIBUTING file, like the one at the top of this repository's own guidelines.

---

## Common Gotchas

- Not installed by default: unlike most tools in this list, `tree` often isn't preinstalled on Linux/macOS and needs a package manager install (`apt install tree`, `brew install tree`).
- Output width: very deep or wide trees can wrap awkwardly in a narrow terminal — `-L` to limit depth or piping through `less -S` helps.

---

## Example Walkthrough

```bash
tree -L 2 -I "node_modules|.git"
```

Shows a clean, two-level overview of a project's folder structure while hiding dependency and version-control clutter — a typical first command when exploring an unfamiliar codebase.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)