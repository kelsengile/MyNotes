[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# Command Prompt (cmd.exe)

Command Prompt is Windows' original command-line interpreter — the direct descendant of MS-DOS's `COMMAND.COM` — providing a text-based interface for running programs, managing files, and scripting on Windows.

Reference: [https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/windows-commands](https://learn.microsoft.com/en-us/windows-server/administration/windows-commands/windows-commands)

---

## What Is Command Prompt?

Command Prompt (`cmd.exe`) has shipped with every version of Windows since Windows NT and Windows 95, evolving from — but remaining largely backward-compatible with — MS-DOS's original command interpreter. It executes both **internal commands** (built directly into `cmd.exe`, like `cd`, `dir`, `copy`) and **external commands** (separate executable programs on disk, like `ping.exe` or `ipconfig.exe`), plus **batch scripts** (`.bat`/`.cmd` files) that string commands together.

Despite PowerShell being Microsoft's modern, more powerful shell (available since 2006 and the default in newer Windows Terminal profiles), Command Prompt remains present on every Windows install for compatibility with decades of existing batch scripts, legacy tooling, and muscle memory.

---

## Core Commands

```
dir                      # List files and folders in the current directory
cd foldername             # Change directory
cd ..                     # Move up one directory level
mkdir foldername          # Create a directory
del filename.txt          # Delete a file
copy source.txt dest.txt  # Copy a file
move source.txt dest\     # Move/rename a file
ren oldname.txt newname.txt  # Rename a file
type file.txt             # Print a file's contents (like Unix cat)
cls                       # Clear the screen
echo Hello World          # Print text
ipconfig                  # Show network configuration
ping example.com          # Test connectivity to a host
tasklist                  # List running processes
taskkill /PID 1234        # Kill a process by ID
systeminfo                # Detailed system configuration report
```

## Useful Modifiers and Built-ins

| Command/Switch | Purpose |
|---|---|
| `dir /s` | Recursive directory listing |
| `dir /a` | Show hidden and system files too |
| `cls` | Clear the screen |
| `echo %VARNAME%` | Print the value of an environment variable |
| `set` | List all environment variables, or set one (`set NAME=value`) |
| `where program.exe` | Find which directory on PATH contains an executable (like Unix `which`) |
| `>` / `>>` | Redirect output to a file (overwrite / append) |
| `\|` | Pipe output from one command into another |
| `&&` | Run the next command only if the previous succeeded |

---

## Batch Scripting

Command Prompt's scripting language, expressed in `.bat`/`.cmd` files, predates and differs substantially from PowerShell's:

```bat
@echo off
set NAME=World
echo Hello, %NAME%!
if exist config.ini (
    echo Config found.
) else (
    echo Config missing!
)
```

Batch files are still widely used for simple automation, legacy build scripts, and Windows installer/setup routines, even though PowerShell can do everything they do and more.

---

## Beyond Basic File Commands

- **Network diagnostics**: `ipconfig /all`, `ping`, `tracert`, `nslookup`, and `netstat` are all run from Command Prompt and remain the standard first tools for Windows network troubleshooting.
- **Process and service management**: `tasklist`, `taskkill`, `sc` (service control), and `net start`/`net stop` manage running programs and Windows services.
- **Disk utilities**: `chkdsk` (check disk for errors), `diskpart` (partition management), `format` all run from an elevated Command Prompt.
- **User and permission management**: `net user`, `whoami`, `icacls` for inspecting and modifying file permissions.
- **System information and repair**: `sfc /scannow` (System File Checker, repairs corrupted system files), `systeminfo`, `wmic` (Windows Management Instrumentation Command-line, now deprecated in favor of PowerShell's `Get-CimInstance`).
- **Elevated ("Run as Administrator") mode** unlocks commands that modify system state, services, or protected files.

---

## Command Prompt vs. PowerShell

| | Command Prompt | PowerShell |
|---|---|---|
| Output | Plain text | Structured .NET objects |
| Scripting language | Batch (limited) | Full scripting language with functions, modules |
| Remoting | None built-in | PowerShell Remoting (WinRM) |
| Extensibility | External programs only | Cmdlets, modules, .NET integration |
| Legacy compatibility | Excellent (DOS-era scripts) | Good, with some batch-specific syntax unsupported |

Most Windows administration guidance today favors PowerShell for anything beyond simple, one-off commands, but Command Prompt persists because of its universal availability and the sheer volume of legacy `.bat` automation still in production use.

---

## Related Tools

- **PowerShell** — Microsoft's modern object-based shell and scripting environment.
- **Windows Terminal** — the modern terminal application that can host Command Prompt, PowerShell, and WSL sessions in tabs.
- **WSL (Windows Subsystem for Linux)** — runs a genuine Linux environment and shell (bash, zsh) alongside Windows.
- **Git Bash** — a bash-compatible shell commonly installed alongside Git for Windows, offering Unix-style commands without WSL.

---

## Example Walkthrough

```
cd C:\Projects\myapp
dir /s *.log > logfiles.txt
del *.tmp
```

Navigates into a project directory, recursively finds every `.log` file and saves the listing to a text file, then cleans up temporary files — a typical simple cleanup/reporting sequence still commonly scripted in batch files on Windows servers.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)