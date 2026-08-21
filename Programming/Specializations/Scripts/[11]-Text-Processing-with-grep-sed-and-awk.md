[Previous](./[10]-Pipes-and-Filters.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[12]-Regular-Expressions.md)

*Text Processing*

# Lesson 11 - Text Processing With grep, sed & awk

## 11.1 grep — Searching Text

```bash
grep "error" log.txt              # lines containing "error"
grep -i "error" log.txt           # case-insensitive
grep -r "TODO" src/                # recursive search through a directory
grep -v "debug" log.txt            # invert match (lines NOT containing "debug")
grep -c "error" log.txt            # count matching lines
```

---

## 11.2 sed — Stream Editor

`sed` transforms text, most commonly for find-and-replace:

```bash
sed 's/foo/bar/' file.txt          # replace first occurrence of foo per line
sed 's/foo/bar/g' file.txt         # replace all occurrences per line
sed -i 's/foo/bar/g' file.txt      # edit the file in place
sed -n '2,4p' file.txt              # print only lines 2 through 4
```

---

## 11.3 awk — Pattern Scanning and Processing

`awk` treats input as rows and columns, making it ideal for structured text:

```bash
awk '{print $1}' file.txt          # print the first column
awk -F',' '{print $2}' data.csv    # use comma as the field separator
awk '$3 > 100 {print $1}' data.txt # print column 1 where column 3 > 100
awk '{sum += $2} END {print sum}' data.txt   # sum a column
```

---

## 11.4 Choosing the Right Tool

| Task | Tool |
|---|---|
| Find lines matching a pattern | `grep` |
| Substitute text | `sed` |
| Process columns / compute values | `awk` |

These three tools are usually combined in pipelines rather than used alone.

---

[Previous](./[10]-Pipes-and-Filters.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[12]-Regular-Expressions.md)
