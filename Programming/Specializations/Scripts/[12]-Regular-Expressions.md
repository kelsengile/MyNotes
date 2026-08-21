[Previous](./[11]-Text-Processing-with-grep-sed-and-awk.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[13]-Writing-Your-First-Automation-Script.md)

*Text Processing*

# Lesson 12 - Regular Expressions

## 12.1 What Is a Regular Expression?

A **regular expression (regex)** is a pattern that describes a set of strings. Regexes are used by `grep`, `sed`, `awk`, and virtually every scripting language to search, validate, and extract text.

---

## 12.2 Core Syntax

| Symbol | Meaning |
|---|---|
| `.` | any single character |
| `*` | zero or more of the previous character |
| `+` | one or more (extended regex) |
| `?` | zero or one (extended regex) |
| `^` | start of line |
| `$` | end of line |
| `[abc]` | any one of a, b, c |
| `[^abc]` | any character except a, b, c |
| `\d` | a digit (in most engines) |
| `(abc)` | a capturing group |
| pipe symbol | matches "a or b" (extended regex, e.g. `cat|dog`) |

---

## 12.3 Using Regex with grep

```bash
grep -E "^[0-9]{3}-[0-9]{4}$" phones.txt   # extended regex, match "123-4567"
grep -oE "[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}" emails.txt  # extract emails
```

`-E` enables **extended** regex syntax (`+`, `?`, `|`, `{}` without escaping). `-o` prints only the matched portion.

---

## 12.4 Practical Tips

- Test regexes incrementally — start simple and add complexity.
- Use a tool like regex101.com to visualize matches while learning.
- Prefer readability over cleverness; an overly compact regex is hard to maintain.
- Basic regex (BRE) and extended regex (ERE) have different escaping rules — check which your tool uses.

---

[Previous](./[11]-Text-Processing-with-grep-sed-and-awk.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[13]-Writing-Your-First-Automation-Script.md)
