[Previous](./[9]-Standard-Input-Output-and-Redirection.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[11]-Text-Processing-with-grep-sed-and-awk.md)

*Text Processing*

# Lesson 10 - Pipes & Filters

## 10.1 What Is a Pipe?

A pipe (`|`) connects the stdout of one command to the stdin of the next, letting you build complex operations from small, single-purpose tools:

```bash
ls -la | grep ".txt" | wc -l
```

This lists files, filters for `.txt` files, and counts them.

---

## 10.2 Common Filter Commands

```bash
sort file.txt              # sort lines alphabetically
sort -n numbers.txt         # sort numerically
uniq                        # remove adjacent duplicate lines
wc -l file.txt              # count lines
head -n 10 file.txt         # first 10 lines
tail -n 10 file.txt         # last 10 lines
tail -f log.txt              # follow a file as it grows
cut -d',' -f1 data.csv       # extract the first comma-separated field
```

---

## 10.3 Combining Filters

```bash
cat access.log | grep "ERROR" | cut -d' ' -f1 | sort | uniq -c | sort -rn
```

This chain finds error lines, extracts a field, and counts how often each unique value occurs, sorted by frequency — a very common log-analysis pattern.

---

[Previous](./[9]-Standard-Input-Output-and-Redirection.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[11]-Text-Processing-with-grep-sed-and-awk.md)
