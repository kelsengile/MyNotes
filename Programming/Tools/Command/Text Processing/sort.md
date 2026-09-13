[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# sort

`sort` arranges the lines of a file or input stream into order — alphabetically by default, but also numerically, by a specific column, or in reverse.

Download: [https://man7.org/linux/man-pages/man1/sort.1.html](https://man7.org/linux/man-pages/man1/sort.1.html)

---

## What Is sort?

Plain text files aren't guaranteed to be in any useful order. `sort` fixes that on demand, without modifying the original file unless you explicitly tell it to — making it a natural fit in the middle of a pipeline, right before tools like `uniq` that expect sorted input.

---

## Core Commands

```bash
sort file.txt                # Sort lines alphabetically
sort -r file.txt              # Sort in reverse order
sort -n file.txt               # Sort numerically instead of alphabetically
sort -k2 file.txt               # Sort by the 2nd column/field
sort -u file.txt                # Sort and remove duplicate lines
sort -t, -k3 -n data.csv        # Sort a CSV numerically by its 3rd comma-separated column
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

## Example Walkthrough

```bash
sort -t, -k2 -nr sales.csv | head -5
```

Sorts a CSV numerically by its second column in descending order, then shows just the top 5 rows — a quick way to find the highest values in a dataset from the command line.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
