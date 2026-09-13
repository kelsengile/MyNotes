[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

# sed (Stream Editor)

`sed` is a stream editor for searching, replacing, and transforming text without opening a file in a text editor. It reads input line by line, applies an editing command to each line, and prints the result.

Download: [https://www.gnu.org/software/sed/](https://www.gnu.org/software/sed/)

---

## What Is sed?

The most common use of `sed` by far is find-and-replace across a file — either printing the modified text to the screen or, with the right flag, editing the file directly. It's a scripting tool, not interactive: you tell it exactly what transformation to apply, and it applies it to every matching line.

---

## Core Commands

```bash
sed 's/old/new/' file.txt          # Replace the first "old" with "new" on each line
sed 's/old/new/g' file.txt         # Replace ALL occurrences of "old" with "new" on each line
sed -i 's/old/new/g' file.txt      # Edit the file in place instead of printing to screen
sed -n '5,10p' file.txt            # Print only lines 5 through 10
sed '/pattern/d' file.txt          # Delete every line matching a pattern
```

The `s/old/new/` syntax is called a "substitution" — `s` for substitute, followed by the pattern to find and the replacement, separated by `/`.

---

## In-Place Editing, Carefully

```bash
sed -i.bak 's/foo/bar/g' config.txt
```

`-i.bak` edits the file directly but keeps a backup copy with a `.bak` extension first — a safer habit than plain `-i`, which overwrites the original with no way back if the substitution goes wrong.

---

## Example Walkthrough

```bash
sed -i.bak 's/localhost/production.example.com/g' config.env
cat config.env
```

Replaces every occurrence of "localhost" with a production hostname across a config file, keeping a backup first, then prints the result to confirm the change.

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
