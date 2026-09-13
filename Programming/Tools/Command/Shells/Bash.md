[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

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

## Example Walkthrough

```bash
export NAME="Ada"
echo "Hello, $NAME"
for i in 1 2 3; do echo "Count: $i"; done
```

Sets an environment variable, prints a greeting using it, then loops through a small list of numbers — the same building blocks used in real Bash scripts.

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
