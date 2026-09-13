[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

# grep (Global Regular Expression Print)

`grep` searches text for lines matching a pattern and prints the matching lines. It's the single most-used text-processing tool on Unix-like systems, and its pattern syntax (regular expressions) shows up across many other tools too.

Download: [https://man7.org/linux/man-pages/man1/grep.1.html](https://man7.org/linux/man-pages/man1/grep.1.html)

---

## What Is grep?

`grep` reads input line by line — from a file, multiple files, or piped from another command — and prints only the lines that match a given pattern. That pattern can be a plain word or a full regular expression for more complex matching.

---

## Core Commands

```bash
grep "error" server.log           # Print lines containing "error"
grep -i "error" server.log        # Case-insensitive search
grep -r "TODO" src/               # Recursively search every file in a folder
grep -n "error" server.log        # Show line numbers alongside matches
grep -v "debug" server.log        # Invert match — show lines that DON'T match
grep -c "error" server.log        # Count matching lines instead of printing them
grep -E "error|warning" server.log  # Use extended regex to match either word
```

---

## Regular Expressions

```bash
grep "^[0-9]" file.txt      # Lines starting with a digit
grep "[a-z]*\.log$" file.txt   # Lines ending in ".log"
grep -E "colou?r" file.txt    # Matches "color" or "colour"
```

`^` anchors to the start of a line, `$` to the end, and `.` matches any single character — a small set of building blocks that covers most everyday searches.

---

## Example Walkthrough

```bash
grep -rn "TODO" src/ --include="*.py"
```

Recursively searches every `.py` file under `src/` for the word "TODO", printing the file name and line number for each match — a common way to track leftover work in a codebase.

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
