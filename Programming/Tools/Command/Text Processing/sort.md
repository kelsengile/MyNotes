[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# sort

`sort` arranges the lines of a file or input stream into order — alphabetically by default, but also numerically, by a specific column, by month name, by human-readable size, or in reverse.

Download: [https://man7.org/linux/man-pages/man1/sort.1.html](https://man7.org/linux/man-pages/man1/sort.1.html)

---

## What Is sort?

Plain text files aren't guaranteed to be in any useful order. `sort` fixes that on demand, without modifying the original file unless you explicitly tell it to — making it a natural fit in the middle of a pipeline, right before tools like `uniq` that expect sorted input. Internally, `sort` reads all of its input, orders it according to the requested comparison rule, and writes the result to standard output; for files too large to fit comfortably in memory, GNU `sort` automatically falls back to an external merge-sort strategy using temporary files, so it scales to very large datasets without extra configuration.

---

## Core Commands

```bash
sort file.txt                # Sort lines alphabetically
sort -r file.txt              # Sort in reverse order
sort -n file.txt               # Sort numerically instead of alphabetically
sort -k2 file.txt               # Sort by the 2nd column/field
sort -u file.txt                # Sort and remove duplicate lines
sort -t, -k3 -n data.csv        # Sort a CSV numerically by its 3rd comma-separated column
sort -h sizes.txt                # Sort "human-readable" sizes like 2K, 1M, 3G correctly
sort -M dates.txt                # Sort by month name (Jan, Feb, Mar...)
sort -f names.txt                # Case-insensitive sort ("fold" upper/lower together)
sort -c file.txt                  # Check whether a file is already sorted (no output if so)
sort -R file.txt                  # Randomly shuffle lines instead of ordering them
sort -o sorted.txt file.txt        # Write output to a file instead of the screen
```

---

## Alphabetical vs. Numerical Sorting

```bash
echo -e "10\n2\n1" | sort
# 1
# 10
# 2

echo -e "10\n2\n1" | sort -n
# 1
# 2
# 10
```

Without `-n`, `sort` compares text character by character, so "10" comes before "2" — a common surprise until you remember to add `-n` for numbers.

---

## Sorting by Field: -k and -t

`sort` doesn't have to sort by the whole line. `-k` selects which field to use as the sort key, and `-t` sets the delimiter that separates fields (default is whitespace):

```bash
sort -t: -k3 -n /etc/passwd     # Sort system accounts by numeric user ID
sort -k1,1 -k2,2n data.txt      # Sort by field 1 alphabetically, then field 2 numerically as a tiebreaker
```

Multiple `-k` options let you define primary and secondary sort keys, similar to a spreadsheet's "sort by column A, then column B."

---

## Stable Sorting and Locale

GNU `sort` is a **stable sort** when `--stable` (or `-s`) is used — meaning lines that compare equal keep their original relative order, which matters when chaining multiple sort passes. Sort order can also be affected by the system's locale setting (`LC_COLLATE`); setting `LC_ALL=C` before a `sort` command forces plain byte-order comparison, which is faster and avoids locale-specific quirks (like accented characters sorting unexpectedly) — a common fix when scripting for consistent, portable results.

---

## sort's Role in Classic Pipelines

`sort` is the connective tissue in some of the most common Unix one-liners: `sort | uniq -c` counts occurrences, `sort -n | tail` finds the largest values, and `sort` before a `diff` or `comm` call ensures both inputs are in a comparable order (`comm`, in fact, requires sorted input to work at all).

---

## Example Walkthrough

```bash
sort -t, -k2 -nr sales.csv | head -5
```

Sorts a CSV numerically by its second column in descending order, then shows just the top 5 rows — a quick way to find the highest values in a dataset from the command line.

```bash
du -sh * | sort -h
```

Lists every item in the current folder with a human-readable size, then sorts by that size correctly (so "2K" sorts before "1M", which a plain alphabetical sort would get wrong) — the standard way to find what's taking up the most space.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)