[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# Bash (Bourne Again SHell)

Bash is the default shell on most Linux distributions and was, for many years, the default on macOS too. It's both an interactive command interpreter and a full scripting language, and it's the shell most tutorials and server documentation assume you're using.

Download: [https://www.gnu.org/software/bash/](https://www.gnu.org/software/bash/)

---

## What Is Bash?

A shell is the program that reads what you type, interprets it, and runs the corresponding commands. Bash extends the older Bourne shell (`sh`) with features like command history, tab completion, arrays, and more powerful scripting constructs, while staying largely backward-compatible with it.

---

## Everyday Commands

```bash
pwd                  # Print the current working directory
ls -la                # List all files, including hidden ones, in long format
cd path/              # Change directory
echo "$HOME"          # Print an environment variable
export VAR=value      # Set an environment variable for this session and its children
history               # Show recently run commands
```

---

## Variables and Quoting

```bash
name="Ada"
echo "Hello, $name"        # Double quotes: variables are expanded
echo 'Hello, $name'        # Single quotes: literal text, no expansion
readonly PI=3.14159         # Constant — cannot be reassigned
unset name                  # Remove a variable
```

The difference between single and double quotes is one of the most important things to internalize in Bash: double quotes allow variable and command expansion inside them, while single quotes treat everything literally, which matters a lot once strings contain `$`, backticks, or spaces.

---

## Scripting Basics

```bash
#!/bin/bash
name="World"
if [ "$name" = "World" ]; then
    echo "Hello, $name!"
fi

for f in *.txt; do
    echo "Found: $f"
done
```

The `#!/bin/bash` line (the "shebang") tells the system which interpreter to run the script with. Scripts need execute permission (`chmod +x script.sh`) before you can run them directly.

---

## Conditionals and Test Operators

```bash
if [ -f "file.txt" ]; then echo "File exists"; fi
if [ -d "folder" ]; then echo "Directory exists"; fi
if [ "$a" -eq "$b" ]; then echo "Numbers equal"; fi
if [ "$a" = "$b" ]; then echo "Strings equal"; fi
if [[ "$name" == a* ]]; then echo "Starts with a"; fi
```

`[[ ]]` (a Bash-specific extension) supports pattern matching and is generally safer than the POSIX `[ ]` test, since it avoids some word-splitting pitfalls with unquoted variables.

---

## Loops

```bash
for i in 1 2 3; do echo "$i"; done
for f in *.log; do echo "$f"; done

count=0
while [ $count -lt 5 ]; do
    echo "$count"
    count=$((count + 1))
done

until [ $count -ge 10 ]; do
    count=$((count + 1))
done
```

---

## Functions

```bash
greet() {
    echo "Hello, $1!"
}
greet "Ada"
```

Function arguments are accessed with `$1`, `$2`, etc. (like a script's own command-line arguments), `$#` gives the argument count, and `$@` expands to all arguments as separate words.

---

## Command Substitution and Pipes

```bash
today=$(date +%F)
echo "Today is $today"

find . -name "*.log" | wc -l
```

`$(command)` runs a command and substitutes its output as a string — the modern replacement for the older backtick syntax `` `command` ``. Pipes (`|`) chain commands together, feeding one command's output as another's input, which is the foundation of most powerful shell one-liners.

---

## Redirection

```bash
command > output.txt      # Redirect stdout, overwriting the file
command >> output.txt     # Redirect stdout, appending
command 2> errors.txt     # Redirect stderr only
command &> all.txt        # Redirect both stdout and stderr
command < input.txt       # Use a file as stdin
```

---

## Special Variables

| Variable | Meaning |
|---|---|
| `$?` | Exit status of the last command (0 = success) |
| `$$` | Process ID of the current shell |
| `$0` | The script or shell's own name |
| `$1`, `$2`... | Positional arguments passed to a script |
| `$#` | Number of positional arguments |
| `$@` | All positional arguments as a list |

---

## Configuration Files

Bash reads `~/.bashrc` for interactive non-login shells (most terminal windows) and `~/.bash_profile` or `~/.profile` for login shells, which is where aliases, custom prompts, and environment variables are typically set up to persist across sessions.

---

## Common Gotchas

- Unquoted variables: `rm $file` can break (or behave dangerously) if `$file` contains spaces or is empty — `rm "$file"` is the safer habit almost everywhere.
- `[ ]` vs `[[ ]]` vs `(( ))`: `[ ]` is POSIX-portable but stricter about quoting; `[[ ]]` is Bash-only but safer for string tests; `(( ))` is for arithmetic comparisons — mixing them up produces confusing errors.

---

## Example Walkthrough

```bash
export NAME="Ada"
echo "Hello, $NAME"
for i in 1 2 3; do echo "Count: $i"; done
```

Sets an environment variable, prints a greeting using it, then loops through a small list of numbers — the same building blocks used in real Bash scripts.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)