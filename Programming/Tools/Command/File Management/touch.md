[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# touch

`touch` creates empty files, or updates the "last modified" timestamp of a file that already exists, without changing its contents.

Download: [https://man7.org/linux/man-pages/man1/touch.1.html](https://man7.org/linux/man-pages/man1/touch.1.html)

---

## What Is touch?

The name comes from its timestamp-updating behavior: "touching" a file marks it as recently modified. As a side effect, running `touch` on a file that doesn't exist yet creates it, empty — which is how most people actually use it day to day.

---

## Core Commands

```bash
touch file.txt                  # Create an empty file, or update its timestamp if it exists
touch file1.txt file2.txt       # Create/touch multiple files at once
touch -t 202501011200 file.txt  # Set a specific timestamp (YYYYMMDDhhmm)
touch -c file.txt                # Only update timestamp if the file already exists; don't create it
```

---

## Why Timestamps Matter

Build tools like `make` decide whether to rebuild a file by comparing timestamps. `touch`ing a source file forces it to appear "newer" than its compiled output, which is a quick way to force a rebuild without actually editing the file's contents.

---

## Example Walkthrough

```bash
touch src/main.c
make
```

Marks `main.c` as just modified even without editing it, so the next `make` run rebuilds it — useful when you know a dependency changed but the timestamp didn't update on its own.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
