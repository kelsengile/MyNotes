[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# grep (Global Regular Expression Print)

`grep` searches text for lines matching a pattern and prints the matching lines. It's the single most-used text-processing tool on Unix-like systems, and its pattern syntax (regular expressions) shows up across dozens of other tools — `sed`, `awk`, `find`, most programming languages, and even GUI text editors.

Download: [https://man7.org/linux/man-pages/man1/grep.1.html](https://man7.org/linux/man-pages/man1/grep.1.html)

---

## History & Origins

`grep` was written by Ken Thompson in 1973 for early Unix at Bell Labs. The name isn't an acronym in the usual sense — it comes from a command in the `ed` line editor, `g/re/p` ("**g**lobally search for a **r**egular **e**xpression and **p**rint the matching lines"). That editor command was used so often that Thompson pulled it out into its own standalone program, and the name stuck. Because it was one of the earliest text tools built into Unix, `grep`'s design — read text a line at a time, apply a pattern, print matches — became the template that later tools like `awk` and `sed` were built around.

Today there are several implementations you'll encounter: GNU grep (the default on most Linux distributions, and the most feature-rich), BSD grep (on macOS and BSD systems), and drop-in replacements like `ripgrep` (`rg`) and `ag` ("the silver searcher") that prioritize raw search speed on large codebases. The examples below assume GNU grep, which covers the vast majority of real-world usage.

---

## What Is grep?

`grep` reads input line by line — from a file, multiple files, or piped from another command — and prints only the lines that match a given pattern. That pattern can be a plain word or a full regular expression for more complex matching. Conceptually, `grep` is a **filter**: text goes in, a subset of that text (the matching lines) comes out. This makes it a natural fit anywhere in a Unix pipeline, since it reads from standard input and writes to standard output by default.

`grep` doesn't understand file formats, syntax trees, or structure — it only sees a stream of lines and checks each one against a pattern. That simplicity is exactly what makes it fast and universally applicable, whether you're searching source code, log files, CSVs, or raw network output.

---

## Core Commands

```bash
grep "error" server.log           # Print lines containing "error"
grep -i "error" server.log        # Case-insensitive search
grep -r "TODO" src/               # Recursively search every file in a folder
grep -n "error" server.log        # Show line numbers alongside matches
grep -v "debug" server.log        # Invert match — show lines that DON'T match
grep -c "error" server.log        # Count matching lines instead of printing them
grep -l "error" *.log             # List only the FILENAMES that contain a match
grep -w "cat" file.txt            # Match "cat" as a whole word only (not "catalog")
grep -A 3 "Exception" app.log     # Show 3 lines of context AFTER each match
grep -B 3 "Exception" app.log     # Show 3 lines of context BEFORE each match
grep -C 3 "Exception" app.log     # Show 3 lines of context on BOTH sides
grep -o "[0-9]\+" file.txt        # Print only the matched text, not the whole line
grep -E "error|warning" server.log  # Use extended regex to match either word
grep -f patterns.txt file.txt     # Read a list of patterns from a file
grep --color=auto "error" server.log  # Highlight matches in color
```

---

## Regular Expressions

`grep` supports two dialects of regular expressions: Basic Regular Expressions (BRE, the default) and Extended Regular Expressions (ERE, enabled with `-E`, historically called `egrep`). ERE treats metacharacters like `|`, `+`, and `?` as special without needing a backslash, which is why most people reach for `-E` by default.

```bash
grep "^[0-9]" file.txt          # Lines starting with a digit
grep "[a-z]*\.log$" file.txt    # Lines ending in ".log"
grep -E "colou?r" file.txt      # Matches "color" or "colour"
grep -E "^(GET|POST) " access.log  # Lines starting with GET or POST
grep -P "\d{3}-\d{4}" file.txt  # Perl-compatible regex (PCRE), if supported
```

Key building blocks:
- `^` anchors to the start of a line, `$` to the end.
- `.` matches any single character.
- `*` means "zero or more of the previous character," `+` means "one or more" (ERE only, or escaped `\+` in BRE).
- `[...]` defines a character class, like `[a-z]` or `[0-9]`.
- `\b` matches a word boundary (GNU extension).

---

## Related Flags and Modes Worth Knowing

- `-z` treats the entire input as one long line separated by null bytes, useful for multi-line matching.
- `-x` requires the whole line, not just part of it, to match the pattern.
- `--include="*.py"` and `--exclude="*.min.js"` filter which files a recursive search touches.
- `zgrep`, `zcat`, and `bzgrep` are companion tools that let you `grep` directly inside `.gz` or `.bz2` compressed files without manually decompressing them first.
- `pgrep` is a completely different, unrelated tool that searches running **processes** by name rather than text in a file — easy to confuse by name alone.

---

## grep in the Wider Toolchain

`grep` is rarely the final step in a real workflow — it's usually the filter in the middle of a pipeline. It's commonly combined with `cut` or `awk` to pull out specific fields from matching lines, with `sort` and `uniq -c` to count how often something occurs, and with `xargs` to feed matching filenames into another command. Modern alternatives like `ripgrep` implement the same conceptual model but add automatic `.gitignore` awareness and multi-threaded search, making them popular for searching large source trees — though the core mental model (pattern in, matching lines out) is identical to classic `grep`.

---

## Example Walkthrough

```bash
grep -rn "TODO" src/ --include="*.py"
```

Recursively searches every `.py` file under `src/` for the word "TODO", printing the file name and line number for each match — a common way to track leftover work in a codebase.

```bash
grep -c "ERROR" app.log
grep -A 2 "FATAL" app.log
```

First counts how many error lines exist in a log without printing them all, then shows any fatal errors along with the two lines of context that follow — often the actual stack trace or cause.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)