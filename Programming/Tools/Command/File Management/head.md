[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# head

`head` prints the first lines (by default, the first 10) of one or more files or of standard input. It's the natural counterpart to `tail`, and together the two are the standard way to peek at large files without opening them entirely.

Reference: [https://man7.org/linux/man-pages/man1/head.1.html](https://man7.org/linux/man-pages/man1/head.1.html)

---

## What Is head?

`head` reads a stream and stops emitting output once it has printed the requested number of lines (or bytes) — it doesn't need to read or buffer the entire file first. This makes it very cheap even on enormous files or infinite streams: `head -n 5` on a 50 GB log file only touches the beginning of that file.

By default it assumes 10 lines because that historically fit nicely on early terminal screens, but this is fully configurable.

---

## Core Commands

```bash
head file.txt                  # Print the first 10 lines
head -n 20 file.txt             # Print the first 20 lines
head -n -5 file.txt             # Print everything except the last 5 lines
head -c 100 file.txt            # Print the first 100 bytes
head -q file1.txt file2.txt     # Suppress the filename headers when given multiple files
head file1.txt file2.txt        # With multiple files, each is preceded by a "==> filename <==" header
```

## Full Option Reference

| Flag | Long form | Meaning |
|---|---|---|
| `-n NUM` | `--lines=NUM` | Print the first NUM lines (a leading `-` means "all but the last NUM") |
| `-c NUM` | `--bytes=NUM` | Print the first NUM bytes instead of lines |
| `-q` | `--quiet`, `--silent` | Never print filename headers, even with multiple files |
| `-v` | `--verbose` | Always print filename headers, even with a single file |
| `-z` | `--zero-terminated` | Treat NUL, not newline, as the line separator |

---

## Piping: head's Most Common Real-World Use

`head` is used constantly at the end of a pipeline to limit output that would otherwise scroll past:

```bash
ls -la | head -n 5              # See just the first few directory entries
ps aux --sort=-%mem | head      # Top memory-hungry processes (after sorting)
history | head -n 20            # First 20 commands ever run in this session
curl -s https://example.com | head -c 500   # Peek at the first 500 bytes of a web response
```

Because it stops reading as soon as it has enough, `head` also acts as a safety valve: piping an infinite or very fast-producing stream (like `yes` or `cat /dev/urandom`) into `head` lets you sample it without hanging or filling your terminal.

---

## Negative Counts: Everything but the End

```bash
head -n -3 file.txt
```

This somewhat unusual syntax prints the whole file *except* the last 3 lines — the mirror image of `tail -n +N`. It requires `head` to know the file's total length, so unlike a plain `head -n 10`, this variant does need to read (or seek) further into the file.

---

## Related Tools

- `tail` — prints the *end* of a file, and supports `-f` to follow it live.
- `less` / `more` — paginated viewers for interactively scrolling through a file.
- `sed -n '1,10p'` / `awk 'NR<=10'` — more powerful but heavier alternatives that can do the same job as part of a larger transformation.
- `wc -l` — count lines in a file, often used alongside `head`/`tail` to decide how much to show.

---

## Example Walkthrough

```bash
head -n 3 access.log
tail -n +4 access.log | head -n 3
```

Shows the first 3 lines of a log file, then — by combining `tail -n +4` (start from line 4 onward) with `head -n 3` — shows lines 4 through 6, a common pattern for paginating through a file's contents from the command line without a dedicated pager.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)