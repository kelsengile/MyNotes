[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# cut

`cut` extracts specific sections — columns or character ranges — from each line of text. It's a lighter-weight alternative to `awk` when all you need is to pull out one or two fields, with a much smaller and simpler set of options.

Download: [https://man7.org/linux/man-pages/man1/cut.1.html](https://man7.org/linux/man-pages/man1/cut.1.html)

---

## What Is cut?

`cut` works on **delimited** text — lines split by a consistent separator like a comma, tab, or colon — or on fixed character/byte positions. It doesn't understand quoted fields or variable-width columns the way a full CSV parser would, so it's best suited to simple, predictable formats. `cut` was designed to do exactly one job well: slice text by position, either by field number or by raw character offset, and nothing more — which is why it starts up fast and composes cleanly in pipelines.

---

## Core Commands

```bash
cut -d, -f2 data.csv          # Extract the 2nd comma-separated field
cut -d: -f1 /etc/passwd       # Extract the 1st colon-separated field (usernames)
cut -d, -f1,3 data.csv         # Extract the 1st and 3rd fields
cut -d, -f2-4 data.csv         # Extract a range of fields, 2 through 4
cut -c1-5 file.txt              # Extract characters 1 through 5 of each line
cut -c5- file.txt               # Extract everything from character 5 to the end
cut -d, -f2 --complement data.csv  # Extract everything EXCEPT the 2nd field
cut -d, --output-delimiter=';' -f1,2 data.csv  # Change the delimiter used in the output
```

`-d` sets the delimiter (default is Tab), and `-f` chooses which field(s) to keep. `-c` switches to character-position mode instead of field mode, which is useful for fixed-width text where columns don't have a consistent separator character at all.

---

## Limitations Worth Knowing

`cut` treats the delimiter character literally, with no awareness of quoting — a CSV field like `"Smith, John"` (a quoted value that legitimately contains a comma) will be split incorrectly, since `cut` has no concept of quoted fields. For real CSV files with quoted or escaped values, a proper CSV-aware tool (or `awk` with more careful field handling, or a scripting language's CSV library) is the safer choice. `cut` is best reserved for simple, well-formed delimited text like `/etc/passwd` or basic log formats.

---

## cut vs. awk

For a simple "give me column 2" task, `cut -d, -f2` is shorter and clearer than the equivalent `awk` command. Once you need conditions, calculations, or field-based filtering (e.g., "print column 2 only where column 3 is greater than 100"), `awk` becomes the better tool, since `cut` has no concept of conditional logic at all.

---

## Example Walkthrough

```bash
cut -d: -f1,3 /etc/passwd | head -5
```

Extracts just the username and user ID fields from the system's password file and shows the first 5 entries — a quick way to see who has accounts on a Linux machine without the extra fields cluttering the view.

```bash
cut -d, -f1,4-6 report.csv > summary.csv
```

Builds a smaller CSV containing only the first column and columns four through six from a larger report — a fast way to trim a wide spreadsheet export down to just the fields that matter, without opening a spreadsheet program.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)