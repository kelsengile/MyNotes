# Lesson 5: Redirection and Piping

CMD commands normally read from the keyboard and write to the screen. Redirection and piping let you change where input comes from and where output goes.

## The redirection operators

| Operator | Meaning |
|----------|---------|
| `>` | Send output to a file, **overwriting** it |
| `>>` | Send output to a file, **appending** to it |
| `<` | Read input from a file instead of the keyboard |
| `\|` | Send the output of one command into the input of another (a "pipe") |

## Examples

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

## Piping between commands

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

## Discarding output

Sometimes you want to run a command but throw away its output (for example, in a script). Redirect to the special `nul` device:
```
ping 8.8.8.8 > nul
```

## Combining redirection and piping

You can chain these together:
```
dir /s | find "Documents" > results.txt
```
Lists everything recursively, filters for lines mentioning "Documents", and saves the result to `results.txt`.

## Try it yourself

1. Run `dir > mylist.txt`, then open `mylist.txt` with `type`.
2. Run `dir | find ".md"` in a folder that has some Markdown files.
3. Run `tasklist | find "svchost"` to see how many `svchost` processes are running.

