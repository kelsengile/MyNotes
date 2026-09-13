[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# find

`find` searches a directory tree for files and folders matching criteria like name, size, type, or modification time — and can act on whatever it finds.

Download: [https://man7.org/linux/man-pages/man1/find.1.html](https://man7.org/linux/man-pages/man1/find.1.html)

---

## What Is find?

Where `grep` searches *inside* file contents, `find` searches for the files themselves, based on their metadata: name, type, size, permissions, and age. It walks an entire directory tree, including nested subfolders, by default.

---

## Core Commands

```bash
find . -name "*.txt"                 # Find all .txt files from the current folder down
find . -type d -name "test*"         # Find directories whose name starts with "test"
find . -type f -size +10M            # Find files larger than 10 MB
find . -mtime -7                     # Find files modified in the last 7 days
find . -name "*.log" -delete         # Find and delete matching files
find . -name "*.tmp" -exec rm {} \;  # Find matching files and run a command on each
```

---

## Acting on Results

The `-exec` flag runs a command on every match, with `{}` as a placeholder for the found file and `\;` marking the end of the command:

```bash
find . -name "*.bak" -exec rm {} \;
```

For simple deletions, `-delete` is a shorter, safer alternative to `-exec rm {} \;`.

---

## Example Walkthrough

```bash
find . -type f -name "*.log" -mtime +30 -delete
```

Finds every `.log` file older than 30 days anywhere in the current directory tree and deletes them in one pass — a common log-cleanup command in maintenance scripts.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
