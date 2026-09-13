[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# tr (translate)

`tr` translates, replaces, or deletes individual characters in a stream of text. It works on characters, not whole words or lines, which makes it a fast, simple tool for small text transformations.

Download: [https://man7.org/linux/man-pages/man1/tr.1.html](https://man7.org/linux/man-pages/man1/tr.1.html)

---

## What Is tr?

`tr` takes two sets of characters and replaces every occurrence of a character from the first set with the corresponding character from the second — or deletes/squeezes characters entirely with the right flag. Unlike `sed`, it has no concept of patterns or whole words, only individual characters.

---

## Core Commands

```bash
tr 'a-z' 'A-Z' < file.txt          # Convert lowercase to uppercase
tr -d '0-9' < file.txt              # Delete all digits
tr -s ' ' < file.txt                 # Squeeze repeated spaces into a single space
tr '\n' ',' < file.txt               # Replace newlines with commas
tr -d '\r' < windows_file.txt        # Strip Windows-style carriage returns
```

`tr` reads from standard input, so it's almost always used with a redirect (`<`) or as part of a pipeline, not given a filename directly.

---

## A Common Fix: Line Ending Conversion

```bash
tr -d '\r' < windows_file.txt > unix_file.txt
```

Windows text files often use `\r\n` line endings, while Unix tools expect plain `\n`. Stripping the `\r` characters is a quick one-line fix for files that display strangely or fail to run as scripts on Linux.

---

## Example Walkthrough

```bash
cat names.txt | tr 'a-z' 'A-Z' | tr -s ' '
```

Converts a list of names to uppercase, then collapses any accidental double spaces into single spaces — a couple of small character-level fixes chained together in one pipeline.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
