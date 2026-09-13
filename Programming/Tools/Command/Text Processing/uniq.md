[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# uniq

`uniq` detects and filters repeated **adjacent** lines in text. It's almost always used together with `sort`, since `uniq` only compares each line to the one directly before it.

Download: [https://man7.org/linux/man-pages/man1/uniq.1.html](https://man7.org/linux/man-pages/man1/uniq.1.html)

---

## What Is uniq?

A common misconception is that `uniq` removes all duplicates in a file. It doesn't — it only collapses consecutive matching lines. Non-adjacent duplicates pass through untouched, which is why the standard pattern is to `sort` first so that identical lines end up next to each other.

---

## Core Commands

```bash
uniq file.txt              # Remove consecutive duplicate lines
uniq -c file.txt            # Prefix each line with how many times it repeated
uniq -d file.txt            # Show only lines that had duplicates
uniq -u file.txt            # Show only lines that appeared exactly once
sort file.txt | uniq        # The standard pattern: sort first, then dedupe
```

---

## Why sort Comes First

```bash
uniq unsorted.txt      # WRONG — misses non-adjacent duplicates
sort unsorted.txt | uniq  # RIGHT — duplicates are now adjacent, so uniq catches them all
```

---

## Example Walkthrough

```bash
awk '{print $1}' access.log | sort | uniq -c | sort -nr | head -10
```

Pulls the first column (commonly an IP address) from a log file, sorts it, counts how many times each value repeats, sorts those counts in descending order, and shows the top 10 — a classic one-liner for finding the most frequent visitors in a web server log.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
