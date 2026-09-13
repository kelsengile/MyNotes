[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# cut

`cut` extracts specific sections — columns or character ranges — from each line of text. It's a lighter-weight alternative to `awk` when all you need is to pull out one or two fields.

Download: [https://man7.org/linux/man-pages/man1/cut.1.html](https://man7.org/linux/man-pages/man1/cut.1.html)

---

## What Is cut?

`cut` works on **delimited** text — lines split by a consistent separator like a comma, tab, or colon — or on fixed character positions. It doesn't understand quoted fields or variable-width columns the way a full CSV parser would, so it's best suited to simple, predictable formats.

---

## Core Commands

```bash
cut -d, -f2 data.csv          # Extract the 2nd comma-separated field
cut -d: -f1 /etc/passwd       # Extract the 1st colon-separated field (usernames)
cut -d, -f1,3 data.csv         # Extract the 1st and 3rd fields
cut -c1-5 file.txt              # Extract characters 1 through 5 of each line
```

`-d` sets the delimiter (default is Tab), and `-f` chooses which field(s) to keep.

---

## cut vs. awk

For a simple "give me column 2" task, `cut -d, -f2` is shorter and clearer than the equivalent `awk` command. Once you need conditions, calculations, or field-based filtering, `awk` becomes the better tool.

---

## Example Walkthrough

```bash
cut -d: -f1,3 /etc/passwd | head -5
```

Extracts just the username and user ID fields from the system's password file and shows the first 5 entries — a quick way to see who has accounts on a Linux machine without the extra fields cluttering the view.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
