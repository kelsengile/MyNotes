[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

# head

`head` prints the beginning of a file or input stream — by default, the first 10 lines. It's a quick way to peek at a file's start without opening the whole thing.

Download: [https://man7.org/linux/man-pages/man1/head.1.html](https://man7.org/linux/man-pages/man1/head.1.html)

---

## What Is head?

Large files — logs, datasets, generated output — are often easiest to understand by just glancing at the first handful of lines: a CSV's header row, a log file's earliest entries, the top of a config file. `head` gives you exactly that, instantly.

---

## Core Commands

```bash
head file.txt              # Print the first 10 lines (default)
head -n 20 file.txt          # Print the first 20 lines
head -n 5 file1.txt file2.txt  # Print the first 5 lines of each file, labeled
head -c 100 file.txt          # Print the first 100 bytes instead of lines
```

---

## head vs. tail

`head` shows the start of a file; `tail` (covered separately) shows the end. Reaching for the right one depends on what you're checking: a CSV's column headers call for `head`; the most recent log entries call for `tail`.

---

## Example Walkthrough

```bash
head -n 1 data.csv
head -n 20 data.csv | column -s, -t
```

Prints just the CSV's header row to see the column names, then previews the first 20 rows formatted as an aligned table.

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
