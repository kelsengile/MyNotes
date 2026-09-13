[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# uniq

`uniq` detects and filters repeated **adjacent** lines in text. It's almost always used together with `sort`, since `uniq` only compares each line to the one directly before it — it has no memory of lines further back.

Download: [https://man7.org/linux/man-pages/man1/uniq.1.html](https://man7.org/linux/man-pages/man1/uniq.1.html)

---

## What Is uniq?

A common misconception is that `uniq` removes all duplicates in a file. It doesn't — it only collapses consecutive matching lines. Non-adjacent duplicates pass through untouched, which is why the standard pattern is to `sort` first so that identical lines end up next to each other. This design isn't a limitation so much as a reflection of `uniq`'s original purpose: it was built to work as a lightweight, single-pass stream filter, not a tool that loads and indexes an entire file's contents in memory.

---

## Core Commands

```bash
uniq file.txt              # Remove consecutive duplicate lines
uniq -c file.txt            # Prefix each line with how many times it repeated
uniq -d file.txt            # Show only lines that had duplicates
uniq -u file.txt            # Show only lines that appeared exactly once
uniq -i file.txt            # Case-insensitive comparison
uniq -f 1 file.txt          # Ignore the first field when comparing lines
uniq -w 10 file.txt         # Compare only the first 10 characters of each line
sort file.txt | uniq        # The standard pattern: sort first, then dedupe
```

---

## Why sort Comes First

```bash
uniq unsorted.txt      # WRONG — misses non-adjacent duplicates
sort unsorted.txt | uniq  # RIGHT — duplicates are now adjacent, so uniq catches them all
```

---

## Skipping Fields and Characters

`-f N` skips the first N whitespace-separated fields before comparing lines, and `-w N` limits the comparison to the first N characters. These are useful when lines share a common prefix or timestamp that shouldn't factor into the duplicate check — for example, comparing log lines while ignoring a leading timestamp column that's different on every line even when the message itself repeats.

---

## uniq vs. sort -u

`sort -u` combines sorting and de-duplication in a single pass and is often slightly more efficient than `sort | uniq` as a separate pipeline, but it only removes exact duplicate lines — it can't do the things `uniq -c`, `uniq -d`, or `uniq -u` do, like counting occurrences or isolating lines that are (or aren't) duplicated. Use `sort -u` for a plain deduplicated list; use `sort | uniq -c` when you need counts.

---

## Example Walkthrough

```bash
awk '{print $1}' access.log | sort | uniq -c | sort -nr | head -10
```

Pulls the first column (commonly an IP address) from a log file, sorts it, counts how many times each value repeats, sorts those counts in descending order, and shows the top 10 — a classic one-liner for finding the most frequent visitors in a web server log.

```bash
sort names.txt | uniq -d
```

Sorts a list of names and prints only the ones that appear more than once — a fast way to spot duplicate entries in a list without writing a script.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)