[Previous](./[6]-Control-Flow-in-Bash.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[8]-Working-with-Files-and-Directories-in-Bash.md)

*Shell Scripting Fundamentals*

# Lesson 7 - Functions In Bash

## 7.1 Defining and Calling Functions

```bash
greet() {
    echo "Hello, $1!"
}

greet "Alice"   # Hello, Alice!
```

Functions must be defined before they are called in the script.

---

## 7.2 Arguments and Return Values

Bash functions don't return values like other languages — they set an **exit status** with `return` (0–255) or print output that can be captured:

```bash
add() {
    echo $(($1 + $2))
}

result=$(add 3 4)
echo "Result: $result"
```

---

## 7.3 Local Variables

By default, variables inside a function are global. Use `local` to scope a variable to the function:

```bash
counter() {
    local i=0
    i=$((i + 1))
    echo "$i"
}
```

This prevents functions from accidentally overwriting variables used elsewhere in the script.

---

[Previous](./[6]-Control-Flow-in-Bash.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[8]-Working-with-Files-and-Directories-in-Bash.md)
