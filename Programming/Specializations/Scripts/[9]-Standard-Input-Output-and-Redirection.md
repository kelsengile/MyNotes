[Previous](./[8]-Working-with-Files-and-Directories-in-Bash.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[10]-Pipes-and-Filters.md)

*Text Processing*

# Lesson 9 - Standard Input, Output & Redirection

## 9.1 The Three Standard Streams

Every process has three standard streams:
- **stdin** (0) — input
- **stdout** (1) — normal output
- **stderr** (2) — error output

Separating stdout and stderr means errors can be handled differently from normal output.

---

## 9.2 Redirection

```bash
echo "hello" > out.txt      # overwrite file with stdout
echo "world" >> out.txt     # append to file
command 2> errors.log       # redirect stderr only
command > out.log 2>&1      # redirect both stdout and stderr to the same file
command < input.txt         # read stdin from a file
command 2>/dev/null         # discard errors
```

---

## 9.3 Here-Documents and Here-Strings

```bash
cat <<EOF
This is line one.
This is line two.
EOF

grep "pattern" <<< "$variable"   # here-string
```

Here-documents are useful for feeding multi-line input to a command without a separate file.

---

[Previous](./[8]-Working-with-Files-and-Directories-in-Bash.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[10]-Pipes-and-Filters.md)
