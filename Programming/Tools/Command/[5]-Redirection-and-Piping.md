[Previous](./[4]-Viewing-And-Creating-File-Content.md) | [Table of Contents](./[0]-Introduction-to-Command.md) | [Next](./[6]-Environment-Variables-And-PATH.md)

# Lesson 5 - Redirection and Piping

CMD commands normally read from the keyboard and write to the screen. Redirection and piping let you change where input comes from and where output goes.

## 5.1 The redirection operators

| Operator | Meaning |
|----------|---------|
| `>` | Send output to a file, **overwriting** it |
| `>>` | Send output to a file, **appending** to it |
| `<` | Read input from a file instead of the keyboard |
| `\|` | Send the output of one command into the input of another (a "pipe") |

---

## 5.2 Examples

Save a directory listing to a file:
```
dir > filelist.txt
```

Add today's date to a log file without erasing previous entries:
```
date /t >> log.txt
```

Feed a list of names into `sort` from a file:
```
sort < names.txt
```

---

## 5.3 Piping between commands

```
dir | sort
```
Lists the current folder and sorts the output alphabetically.

```
dir | find "txt"
```
Lists the current folder, then filters to only lines containing "txt".

```
tasklist | find "chrome"
```
Lists running processes, then filters down to anything with "chrome" in the name — useful for checking if a program is running.

---

## 5.4 Discarding output

Sometimes you want to run a command but throw away its output (for example, in a script). Redirect to the special `nul` device:
```
ping 8.8.8.8 > nul
```

---

## 5.5 Combining redirection and piping

You can chain these together:
```
dir /s | find "Documents" > results.txt
```
Lists everything recursively, filters for lines mentioning "Documents", and saves the result to `results.txt`.

---

[Previous](./[4]-Viewing-And-Creating-File-Content.md) | [Table of Contents](./[0]-Introduction-to-Command.md) | [Next](./[6]-Environment-Variables-And-PATH.md)
