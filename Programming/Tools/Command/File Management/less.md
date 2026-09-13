[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# less

`less` is a terminal pager for viewing file contents (or piped output) one screen at a time, with the ability to scroll in both directions, search, and jump around without loading the whole file into memory.

Download: [https://www.greenwoodsoftware.com/less/](https://www.greenwoodsoftware.com/less/)

---

## What Is less?

`less` was written as an improvement on the older `more` pager — its name is a joke ("less is more"). Unlike `cat`, which dumps everything at once, `less` displays a page at a time and lets you navigate freely. Unlike `more`, `less` can scroll backward, doesn't need to read the entire input before displaying it, and works well as the destination of a long pipeline.

---

## Core Commands

```bash
less file.txt              # Open a file for paged viewing
command | less              # View piped output a page at a time
less +F file.txt            # Start in follow mode (like tail -f)
less -N file.txt            # Show line numbers
less -S file.txt            # Don't wrap long lines; scroll horizontally instead
```

---

## Navigation Inside less

| Key | Action |
|---|---|
| `Space` / `f` | Next page |
| `b` | Previous page |
| `↓` / `j` | Scroll down one line |
| `↑` / `k` | Scroll up one line |
| `g` | Jump to the start of the file |
| `G` | Jump to the end of the file |
| `/pattern` | Search forward for a pattern |
| `?pattern` | Search backward for a pattern |
| `n` | Repeat the last search, same direction |
| `N` | Repeat the last search, opposite direction |
| `q` | Quit |

---

## Searching Inside a File

```
/error
n
n
N
```

Typing `/error` and pressing Enter jumps to the first match; `n` jumps to the next occurrence, `N` jumps back to the previous one — a fast way to step through every occurrence of a pattern in a large log without leaving the pager.

---

## Following a Growing File

```bash
less +F app.log
```

`+F` puts `less` into a mode functionally similar to `tail -f`, following new lines as they're appended. Unlike plain `tail -f`, pressing `Ctrl+C` inside this mode drops back into normal `less` navigation instead of exiting entirely, so you can pause and scroll back through history, then press `F` again to resume following.

---

## Marking and Jumping

```
ma        (set mark "a" at current position)
'a        (jump back to mark "a")
```

Marks let you bookmark a spot in a large file, explore elsewhere, and jump straight back — useful when comparing two sections of a big log or config file.

---

## Useful Startup Flags

| Flag | Meaning |
|---|---|
| `-N` | Show line numbers |
| `-S` | Chop (don't wrap) long lines |
| `-i` | Case-insensitive search |
| `-X` | Don't clear the screen on exit, leaving output visible |
| `-R` | Correctly display raw ANSI color codes (needed for colorized tool output) |

---

## Common Gotchas

- Colored output disappearing: piping a command that outputs color codes (like `grep --color`) into `less` without `-R` shows garbled escape sequences instead of real color.
- Forgetting how to quit: like Vim, `less` is modal in the sense that typing doesn't do anything until you know the right key — `q` is the one to remember.

---

## Example Walkthrough

```bash
grep --color=always "ERROR" app.log | less -R
```

Filters a log for error lines while preserving `grep`'s highlight coloring, then pages through the results in `less` instead of dumping everything to the terminal at once.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
EOF

cat > "/home/claude/work/File Management/mkdir.md" << 'EOF'
[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# mkdir (make directory)

`mkdir` creates new directories.

Download: [https://man7.org/linux/man-pages/man1/mkdir.1.html](https://man7.org/linux/man-pages/man1/mkdir.1.html)

---

## What Is mkdir?

`mkdir` fails by default if a parent directory in the given path doesn't exist yet, or if the target directory already exists — both of these default behaviors are commonly overridden with flags, since scripts often want to create a whole nested path in one go without erroring on existing folders.

---

## Core Commands

```bash
mkdir new_folder                      # Create a single directory
mkdir -p path/to/deep/folder          # Create all missing parent directories as needed
mkdir folder1 folder2 folder3         # Create multiple directories at once
mkdir -m 700 private_folder           # Create with specific permissions immediately
mkdir -v new_folder                   # Verbose — print a message for each directory created
```

---

## All Major Options

| Flag | Meaning |
|---|---|
| `-p` | Create parent directories as needed; don't error if the target already exists |
| `-m mode` | Set permissions (octal) at creation time, rather than `chmod` afterward |
| `-v` | Print a message for each directory created |

---

## Why -p Matters

```bash
mkdir project/src/components        # Fails if project/ or project/src/ don't already exist
mkdir -p project/src/components     # Creates project/, project/src/, and project/src/components/ in one call
```

`-p` is close to essential in scripts that scaffold new project structures, since it removes the need to create and check for each intermediate directory manually.

---

## Creating Multiple Directories With Brace Expansion

```bash
mkdir -p project/{src,tests,docs,build}
```

Combined with the shell's brace expansion (not a `mkdir` feature itself, but commonly used alongside it), this creates several sibling directories in one command — a fast way to scaffold a standard project layout.

---

## Setting Permissions at Creation Time

```bash
mkdir -m 700 ~/.secrets
```

Creating a directory with `-m` avoids a brief window where the directory exists with default (often more permissive) permissions before a follow-up `chmod` is run — relevant for directories meant to hold sensitive data from the moment they're created.

---

## Common Gotchas

- Existing directory errors: without `-p`, running `mkdir` on a directory that already exists produces an error and (in a script) can halt execution unexpectedly — `mkdir -p` is often used defensively even for single directories for this reason.
- umask interaction: the actual permissions of a newly created directory are affected by the shell's `umask` setting unless `-m` explicitly overrides it.

---

## Example Walkthrough

```bash
mkdir -p ~/projects/myapp/{src,tests,docs}
tree ~/projects/myapp
```

Scaffolds a new project's folder structure in one command, then uses `tree` to visually confirm the layout was created correctly.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
EOF

cat > "/home/claude/work/File Management/mv.md" << 'EOF'
[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# mv (move)

`mv` moves or renames files and directories.

Download: [https://man7.org/linux/man-pages/man1/mv.1.html](https://man7.org/linux/man-pages/man1/mv.1.html)

---

## What Is mv?

There's no separate "rename" command on Unix-like systems — renaming a file is just moving it to a new name in the same directory, which is exactly what `mv oldname newname` does. When the source and destination are on the same filesystem, `mv` is nearly instantaneous because it just updates the directory entry rather than copying data; moving across filesystems (e.g. to a different disk or mounted drive) requires an actual copy followed by deleting the original, which `mv` does automatically and transparently.

---

## Core Commands

```bash
mv oldname.txt newname.txt         # Rename a file
mv file.txt folder/                # Move a file into a folder
mv folder1/ folder2/                # Move (or rename) an entire directory
mv -i file.txt existing.txt         # Prompt before overwriting
mv -n file.txt existing.txt         # Never overwrite an existing file
mv -v file.txt folder/              # Verbose — print what's being moved
```

---

## All Major Options

| Flag | Meaning |
|---|---|
| `-i` | Interactive — prompt before overwriting an existing file |
| `-n` | Never overwrite an existing destination |
| `-u` | Update — move only if source is newer, or destination missing |
| `-v` | Verbose — print each move as it happens |
| `-b` | Back up any existing destination file before overwriting it |
| `-T` | Treat destination as a normal file, not a directory (useful for exact renames) |

---

## Moving Multiple Files

```bash
mv file1.txt file2.txt file3.txt destination/
mv *.jpg photos/
```

As with `cp`, when multiple sources are given, the last argument must be an existing directory.

---

## Preventing Accidental Overwrites

```bash
mv -i draft.txt final.txt
```

Because `mv` silently overwrites an existing destination by default (there's no "trash" to recover from), `-i` is a common safety habit, particularly before scripted bulk renames.

---

## Batch Renaming Patterns

```bash
for f in *.jpeg; do mv "$f" "${f%.jpeg}.jpg"; done
```

`mv` itself only moves one source to one destination (or many sources into one directory) — bulk renaming with pattern substitution, like changing every file's extension, is typically done by looping over `mv` in the shell, since `mv` has no built-in wildcard-rename syntax of its own.

---

## Common Gotchas

- No undo: `mv` overwrites destinations without keeping a backup unless `-b` or `-i` is used — there is no built-in "are you sure" beyond those flags.
- Trailing slash ambiguity: `mv folder1 folder2` behaves differently depending on whether `folder2` already exists (renames vs. moves `folder1` inside it) — checking first with `ls` avoids surprises.

---

## Example Walkthrough

```bash
mv -i report_draft.txt report_final.txt
mv *.pdf ~/Documents/Reports/
```

Renames a draft file to a final name (with a safety prompt), then moves every PDF in the current directory into a dedicated reports folder.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)