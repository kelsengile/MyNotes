[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# Command Prompt (CMD)

Command Prompt is the traditional command-line shell built into Windows, dating back to the earliest versions of the OS. It uses its own command language (batch), distinct from Unix shells like Bash.

Download: [https://learn.microsoft.com/windows-server/administration/windows-commands/windows-commands](https://learn.microsoft.com/windows-server/administration/windows-commands/windows-commands)

---

## What Is CMD?

CMD is Windows' original command interpreter, used both interactively and for running `.bat` batch scripts. It's simpler and older than PowerShell, but many legacy tools, installers, and system utilities still expect it — and it starts up faster with a smaller footprint.

---

## Everyday Commands

```cmd
cd folder                    # Change directory
dir                          # List files in the current directory
copy file.txt backup.txt     # Copy a file
del file.txt                 # Delete a file
cls                          # Clear the screen
echo %PATH%                  # Print an environment variable
```

The full lesson series in this Topic — [Getting Started](../[1]-Getting-Started.md) through [Batch Scripting Control Flow](../[13]-Batch-Scripting-Control-Flow.md) — covers CMD in depth.

---

## CMD vs. PowerShell

Microsoft now considers PowerShell the primary Windows shell, but CMD remains installed everywhere for compatibility. PowerShell can run most CMD commands too, but CMD **cannot** run PowerShell-only commands (cmdlets), so the relationship is one-directional.

---

## Example Walkthrough

```cmd
mkdir project
cd project
echo Hello, World! > readme.txt
type readme.txt
```

Creates a new folder, moves into it, writes a line of text into a file using redirection, then displays the file's contents.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
