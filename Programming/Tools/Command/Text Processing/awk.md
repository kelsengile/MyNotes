[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

# awk

`awk` is a text-processing language built around scanning input line by line, splitting each line into fields, and running actions based on patterns. It's especially good at working with structured, column-based text like CSVs or log files.

Download: [https://www.gnu.org/software/gawk/](https://www.gnu.org/software/gawk/)

---

## What Is awk?

Where `grep` finds matching lines and `sed` transforms text, `awk` treats each line as a record split into fields (`$1`, `$2`, etc., by default separated by whitespace), and lets you write logic — conditions, loops, calculations — around those fields.

---

## Core Commands

```bash
awk '{print $1}' file.txt              # Print the first column of every line
awk -F, '{print $2}' data.csv          # Use a comma as the field separator
awk '{print $1, $3}' file.txt          # Print the 1st and 3rd columns
awk '$3 > 100 {print $0}' data.txt      # Print full lines where column 3 is over 100
awk '{sum += $2} END {print sum}' data.txt   # Sum a column and print the total
```

`$0` refers to the entire line; `$1`, `$2`, and so on refer to individual fields.

---

## Patterns and Actions

An `awk` program is a series of `pattern { action }` pairs. If the pattern matches, the action runs for that line. A pattern-less action runs on every line; an action-less pattern just prints matching lines, similar to `grep`.

```bash
awk '/error/ {count++} END {print count}' server.log
```

Counts how many lines contain "error" by incrementing a counter for each match, then prints the final total once the whole file has been read.

---

## Example Walkthrough

```bash
awk -F, 'NR==1 {next} {sum += $3} END {print "Total:", sum}' sales.csv
```

Skips the CSV's header row (`NR==1 {next}`), adds up the third column across every remaining row, and prints the total — a one-liner replacement for opening the file in a spreadsheet just to sum a column.

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
