[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# sed (Stream Editor)

`sed` is a stream editor for searching, replacing, and transforming text without opening a file in a text editor. It reads input line by line, applies an editing command to each line, and prints the result. It is one of the oldest and most consistently available text-processing tools across every Unix-like system, which makes it a reliable choice for scripts that need to run unmodified on many machines.

Download: [https://www.gnu.org/software/sed/](https://www.gnu.org/software/sed/)

---

## History & Origins

`sed` was written by Lee E. McMahon at Bell Labs in 1973–74, built on the same underlying regular expression engine as `grep` and the `ed` line editor that came before both of them. The idea was to take the editing commands people already typed interactively into `ed` and let them run automatically, without a human at the keyboard, across an entire stream of text. That heritage explains why `sed`'s substitution syntax (`s/old/new/`) matches the syntax you'd type inside `ed` or `vi`'s command-line mode — they all descend from the same editing language.

GNU `sed` (the version on most Linux systems) adds many extensions beyond the original POSIX specification, including in-place editing (`-i`), extended regex support (`-E`), and case-conversion escapes. macOS and BSD systems ship an older, stricter `sed` where some flags (notably `-i`) behave slightly differently, which is a frequent source of "works on Linux, breaks on Mac" script bugs.

---

## What Is sed?

The most common use of `sed` by far is find-and-replace across a file — either printing the modified text to the screen or, with the right flag, editing the file directly. It's a scripting tool, not interactive: you tell it exactly what transformation to apply, and it applies it to every matching line without asking for confirmation. Conceptually, `sed` works like a tiny editing program: it reads one line at a time into an internal "pattern space," runs your commands against that line, prints the result (unless told not to), and moves to the next line.

Because `sed` operates line by line by default, it's excellent at text substitution, deletion, and simple line-based selection — but it struggles with tasks that require understanding a whole file's structure at once, which is where a tool like `awk` or a proper scripting language takes over.

---

## Core Commands

```bash
sed 's/old/new/' file.txt          # Replace the first "old" with "new" on each line
sed 's/old/new/g' file.txt         # Replace ALL occurrences of "old" with "new" on each line
sed -i 's/old/new/g' file.txt      # Edit the file in place instead of printing to screen
sed -n '5,10p' file.txt            # Print only lines 5 through 10
sed '/pattern/d' file.txt          # Delete every line matching a pattern
sed '3d' file.txt                  # Delete line 3 specifically
sed '2i\New line here' file.txt    # Insert a new line before line 2
sed '2a\New line here' file.txt    # Append a new line after line 2
sed 's/old/new/2' file.txt         # Replace only the 2nd occurrence per line
sed -E 's/(cat|dog)/pet/g' file.txt  # Use extended regex for grouping/alternation
```

The `s/old/new/` syntax is called a "substitution" — `s` for substitute, followed by the pattern to find and the replacement, separated by `/`. That separator doesn't have to be `/`; when your pattern itself contains slashes (like a file path), you can use another character, e.g. `sed 's|/old/path|/new/path|'`.

---

## In-Place Editing, Carefully

```bash
sed -i.bak 's/foo/bar/g' config.txt
```

`-i.bak` edits the file directly but keeps a backup copy with a `.bak` extension first — a safer habit than plain `-i`, which overwrites the original with no way back if the substitution goes wrong. Note the portability trap: GNU `sed` accepts `-i.bak` with no space, while BSD/macOS `sed` requires the suffix as a separate argument (`sed -i '.bak' ...`), and plain `-i` with no suffix on macOS requires an explicit empty string (`sed -i '' ...`).

---

## Beyond Substitution: sed as a Scripting Language

`sed` has its own small command set beyond `s`, `d`, `p`, `i`, and `a`:

- **Address ranges** — commands can target specific lines or ranges: `sed '10,20d'` deletes lines 10–20; `sed '/START/,/END/d'` deletes everything between two markers, inclusive.
- **Multiple commands** — chain several edits with `-e`: `sed -e 's/foo/bar/' -e 's/baz/qux/' file.txt`.
- **Scripts from a file** — complex, multi-step edits can be written into a `.sed` script file and run with `sed -f script.sed file.txt`, which is easier to maintain than a long one-liner.
- **Hold space** — an advanced feature (`h`, `H`, `g`, `G`, `x`) that lets `sed` remember text from one line and use it while processing a later line, enabling things like reversing a file's line order entirely with `sed`.

---

## sed vs. awk vs. grep

These three form the classic Unix text-processing trio, and each has a distinct focus: `grep` finds lines; `sed` transforms lines (substitute, delete, insert); `awk` treats lines as structured records with fields and supports arithmetic, variables, and control flow. A task like "replace a word everywhere" is `sed`'s job; "sum a column" is `awk`'s job; "find which lines mention an error" is `grep`'s job. Real-world one-liners frequently chain all three together in a pipeline.

---

## Example Walkthrough

```bash
sed -i.bak 's/localhost/production.example.com/g' config.env
cat config.env
```

Replaces every occurrence of "localhost" with a production hostname across a config file, keeping a backup first, then prints the result to confirm the change.

```bash
sed -n '/BEGIN/,/END/p' log.txt
```

Prints only the block of lines that falls between a line containing "BEGIN" and the next line containing "END" — a quick way to extract a specific section from a large log without writing a full parser.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)