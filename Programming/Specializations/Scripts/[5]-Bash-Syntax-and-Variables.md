[Previous](./[4]-Introduction-to-the-Command-Line.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[6]-Control-Flow-in-Bash.md)

*Shell Scripting Fundamentals*

# Lesson 5 - Bash Syntax And Variables

## 5.1 The Shebang Line

Every Bash script should start with a **shebang** telling the system which interpreter to use:

```bash
#!/bin/bash
echo "Hello, world!"
```

Make the script executable and run it:

```bash
chmod +x script.sh
./script.sh
```

---

## 5.2 Variables

Bash variables are untyped and assigned with `=` (no spaces around it):

```bash
name="Alice"
count=5
echo "Hello, $name. Count is $count"
```

Use `${var}` when you need to disambiguate the variable name from surrounding text, e.g. `"${name}s"`.

---

## 5.3 Quoting

Quoting matters a lot in Bash:

```bash
echo $name        # unquoted: word-splitting and globbing can occur
echo "$name"       # double-quoted: variable expands, spaces preserved
echo '$name'       # single-quoted: no expansion at all, literal text
```

As a rule of thumb, always double-quote variables unless you specifically need word-splitting.

---

## 5.4 Command Substitution and Arithmetic

```bash
current_dir=$(pwd)          # command substitution
files=$(ls | wc -l)

sum=$((5 + 3))               # arithmetic expansion
echo "Sum is $sum"
```

---

[Previous](./[4]-Introduction-to-the-Command-Line.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[6]-Control-Flow-in-Bash.md)
