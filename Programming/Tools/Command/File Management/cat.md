[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# cat (concatenate)

`cat` reads files and prints their contents, and can also join multiple files together into one stream. It's one of the simplest Unix tools, but its ability to read/write standard input and output makes it a building block in countless pipelines.

Download: [https://man7.org/linux/man-pages/man1/cat.1.html](https://man7.org/linux/man-pages/man1/cat.1.html)

---

## What Is cat?

`cat`'s name comes from "concatenate" — its original purpose is joining files end to end. Printing a single file's contents to the terminal is really just the one-file case of that same behavior. Because `cat` reads from stdin when given no filename, and writes to stdout by default, it fits naturally into shell pipelines as a source or a pass-through.

---

## Core Commands

```bash
cat file.txt                  # Print a file's contents
cat file1.txt file2.txt       # Print multiple files back to back
cat file1.txt file2.txt > combined.txt   # Concatenate files into a new file
cat -n file.txt                # Print with line numbers
cat -A file.txt                # Show non-printing characters (tabs, line endings)
cat >> file.txt                # Append typed input to a file until Ctrl+D
```

---

## All Major Options

| Flag | Meaning |
|---|---|
| `-n` | Number all output lines |
| `-b` | Number only non-blank lines |
| `-s` | Squeeze repeated blank lines into one |
| `-A` | Show all non-printing characters (`^I` for tabs, `$` at line ends) |
| `-E` | Show `$` at the end of each line only |
| `-T` | Show tabs as `^I` |
| `-v` | Display non-printing characters using `^` and `M-` notation |

---

## Creating Files On the Fly

```bash
cat > notes.txt
This is line one.
This is line two.
[Ctrl+D]
```

With no input file and redirected output, `cat` reads from the keyboard until it receives an end-of-file signal (Ctrl+D), making it a quick way to create a short file without opening an editor.

---

## Using cat in Pipelines

```bash
cat access.log | grep "ERROR" | wc -l
```

This is the classic (and often criticized) "useless use of cat" — `grep "ERROR" access.log | wc -l` does the same thing without `cat`. `cat` is genuinely needed, though, when combining multiple files before further processing, since `grep` alone can't merge several files into a single ordered stream the way `cat file1 file2 |` can.

---

## Viewing Hidden Whitespace

```bash
cat -A script.sh
```

Files edited on Windows sometimes carry `\r\n` line endings that render as `^M$` under `-A`, which is a fast way to diagnose "why does this script fail with a weird error" issues caused by mismatched line-ending conventions.

---

## Common Gotchas

- Large files: `cat`-ing a huge file dumps it all to the terminal at once with no pagination — `less` is almost always the better choice for reading, reserving `cat` for short files or piping.
- Binary files: running `cat` on a binary file can print garbage (or even affect your terminal's display settings) — `file` first, or `xxd`/`hexdump` for binary inspection, is safer.

---

## Example Walkthrough

```bash
cat header.txt body.txt footer.txt > report.txt
cat -n report.txt | head
```

Joins three files into one combined report, then previews the first few lines with line numbers to confirm the merge worked correctly.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
EOF

cat > "/home/claude/work/File Management/cp.md" << 'EOF'
[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# cp (copy)

`cp` copies files and directories from one location to another.

Download: [https://man7.org/linux/man-pages/man1/cp.1.html](https://man7.org/linux/man-pages/man1/cp.1.html)

---

## What Is cp?

At its simplest, `cp source destination` makes a new file at `destination` with the same content as `source`. By default, `cp` does **not** copy directories, and it does **not** preserve metadata like timestamps and permissions unless told to — both behaviors are opt-in through flags, which is a common source of surprise for newcomers.

---

## Core Commands

```bash
cp file.txt backup.txt          # Copy a file to a new name
cp file.txt folder/             # Copy a file into a folder, keeping its name
cp -r folder1/ folder2/          # Recursively copy a directory and its contents
cp -v file.txt backup.txt        # Verbose — print what's being copied
cp -i file.txt existing.txt      # Prompt before overwriting an existing file
cp -u file.txt destination/      # Only copy if source is newer than destination
```

---

## All Major Options

| Flag | Meaning |
|---|---|
| `-r` / `-R` | Recursive — required for copying directories |
| `-i` | Interactive — confirm before overwriting |
| `-n` | Never overwrite an existing file |
| `-u` | Update — copy only if source is newer, or destination missing |
| `-v` | Verbose — print each file as it's copied |
| `-p` | Preserve mode, ownership, and timestamps |
| `-a` | Archive — recursive plus preserves almost everything (shorthand for `-dR --preserve=all`) |
| `-l` | Create hard links instead of copying data |
| `-s` | Create symbolic links instead of copying data |

---

## The -a (Archive) Flag

```bash
cp -a project/ project-backup/
```

`-a` is the flag most people actually want for backing up a directory: it recurses, preserves permissions, ownership, timestamps, and symbolic links exactly as they are, rather than `cp -r` alone, which resets some of that metadata to defaults.

---

## Copying Multiple Files

```bash
cp file1.txt file2.txt file3.txt destination/
cp *.jpg photos/
```

When multiple sources are given, the final argument must be an existing directory — `cp` will error out rather than guess if you try to copy several files to a single non-directory destination.

---

## Preventing Accidental Overwrites

```bash
cp -i important.conf important.conf.bak
```

Because `cp` silently overwrites an existing destination file by default, `-i` (or aliasing `cp` to `cp -i` in your shell config) is a common safety habit, especially before scripting bulk copy operations.

---

## Preserving Just Specific Attributes

```bash
cp --preserve=timestamps file.txt copy.txt
cp --preserve=mode,ownership file.txt copy.txt
```

`--preserve` accepts a comma-separated list (`mode`, `ownership`, `timestamps`, `links`, `context`, `xattr`), letting you preserve exactly the metadata that matters for a given copy instead of all-or-nothing.

---

## Common Gotchas

- Trailing slashes matter: `cp -r folder1 folder2` (no trailing slash on `folder1`) copies `folder1` itself as a subdirectory of `folder2`, while `cp -r folder1/ folder2/` copies its *contents* into `folder2` — behavior can differ slightly by platform, so testing with `-v` first avoids surprises.
- Symlinks: by default `cp` follows symlinks and copies the target file's content; `-P` (no-dereference) copies the symlink itself instead.

---

## Example Walkthrough

```bash
cp -av project/ /backups/project-$(date +%F)/
```

Creates a fully metadata-preserving, date-stamped backup copy of a project directory — a common one-line backup pattern.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)