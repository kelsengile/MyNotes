[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# awk

`awk` is a full text-processing **language** built around scanning input line by line, splitting each line into fields, and running actions based on patterns. It's especially good at working with structured, column-based text like CSVs, log files, or command output — and unlike most of the tools around it, it includes real variables, arithmetic, loops, and even user-defined functions.

Download: [https://www.gnu.org/software/gawk/](https://www.gnu.org/software/gawk/)

---

## History & Origins

`awk` was created in 1977 at Bell Labs by Alfred Aho, Peter Weinberger, and Brian Kernighan — the name is simply their three initials. It was designed to sit between `grep` (find lines) and a full programming language like C: something powerful enough for real data-processing logic, but still a one-liner-friendly tool you could type directly at a shell prompt. A significantly expanded version, "One True AWK" (`nawk`), followed in 1985, and today most Linux systems ship **GNU awk** (`gawk`), which adds networking, better internationalization, and many built-in functions beyond the POSIX standard. `mawk` is a smaller, faster alternative sometimes used when raw throughput matters more than extra features.

`awk`'s influence is broad — its pattern-action structure and field-splitting model directly inspired later tools and even shaped how some scripting languages think about line-oriented text processing.

---

## What Is awk?

Where `grep` finds matching lines and `sed` transforms text, `awk` treats each line as a **record** split into **fields** (`$1`, `$2`, etc., by default separated by whitespace), and lets you write logic — conditions, loops, calculations, string manipulation — around those fields. An `awk` program is a series of `pattern { action }` pairs, evaluated for every input line, making it equally suited to quick one-liners and small standalone scripts saved to a `.awk` file.

Internally, `awk` maintains built-in variables you can read and use in any program: `$0` (the whole current line), `NR` (the current line/record number across all input), `NF` (the number of fields in the current line), and `FS`/`OFS` (input/output field separators).

---

## Core Commands

```bash
awk '{print $1}' file.txt              # Print the first column of every line
awk -F, '{print $2}' data.csv          # Use a comma as the field separator
awk '{print $1, $3}' file.txt          # Print the 1st and 3rd columns
awk '$3 > 100 {print $0}' data.txt      # Print full lines where column 3 is over 100
awk '{sum += $2} END {print sum}' data.txt   # Sum a column and print the total
awk '{print NR, $0}' file.txt          # Prefix every line with its line number
awk '{print NF}' file.txt              # Print how many fields each line has
awk 'NF > 0' file.txt                  # Print only non-empty lines
awk 'BEGIN {print "Report:"} {print} END {print "Done."}' file.txt
```

`$0` refers to the entire line; `$1`, `$2`, and so on refer to individual fields. `BEGIN` and `END` blocks run once before any input is read and once after all input is processed, respectively — useful for printing headers, initializing variables, or printing final totals.

---

## Patterns and Actions

An `awk` program is a series of `pattern { action }` pairs. If the pattern matches, the action runs for that line. A pattern-less action runs on every line; an action-less pattern just prints matching lines, similar to `grep`.

```bash
awk '/error/ {count++} END {print count}' server.log
```

Counts how many lines contain "error" by incrementing a counter for each match, then prints the final total once the whole file has been read.

---

## awk as a Real Programming Language

Beyond one-liners, `awk` supports the building blocks of a general-purpose language, which is often underused:

- **Variables and arithmetic** — `{total += $2; count++}` accumulates values across lines with no special setup.
- **Control flow** — `if`/`else`, `while`, `for` loops all work inside actions, e.g. `for (i=1; i<=NF; i++) print $i`.
- **String functions** — `length()`, `substr()`, `split()`, `gsub()`, `sprintf()`, and `toupper()`/`tolower()` cover most text manipulation needs without leaving `awk`.
- **Associative arrays** — `awk` arrays are keyed by string, making them natural for counting or grouping: `count[$1]++` tallies occurrences of the first column, similar to a hash map.
- **User-defined functions** — larger `awk` scripts can define reusable functions with `function name(args) { ... }`.
- **Multiple field/record separators** — `RS` (record separator) can be changed from the default newline to process paragraph-based or otherwise non-line-oriented input.

---

## Example Walkthrough

```bash
awk -F, 'NR==1 {next} {sum += $3} END {print "Total:", sum}' sales.csv
```

Skips the CSV's header row (`NR==1 {next}`), adds up the third column across every remaining row, and prints the total — a one-liner replacement for opening the file in a spreadsheet just to sum a column.

```bash
awk -F, '{count[$1]++} END {for (ip in count) print count[ip], ip}' access.csv | sort -rn | head
```

Uses an associative array to tally how many times each value in the first column (say, an IP address) appears, then prints the counts — the same idea behind `sort | uniq -c`, done natively inside a single `awk` program.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)