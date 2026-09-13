[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# Zsh (Z Shell)

Zsh is an extended, highly customizable shell that's largely compatible with Bash while adding richer completion, globbing, and interactive features. It's been the default shell on macOS since Catalina (2019).

Download: [https://www.zsh.org/](https://www.zsh.org/)

---

## What Is Zsh?

Zsh started as an enhancement of the Bourne shell family, similar in spirit to Bash, but developed independently with a focus on interactive usability: smarter tab completion, spelling correction, more powerful globbing (pattern matching for filenames), and a huge plugin ecosystem (most famously **Oh My Zsh**) for customizing the prompt and adding features. Scripts written for Bash mostly run under Zsh too, though there are subtle incompatibilities (like array indexing starting at 1 instead of 0) that can trip up scripts moved between the two without adjustment.

---

## Everyday Commands

Zsh supports all the same basic navigation and utility commands as Bash (`cd`, `ls`, `pwd`, `export`, etc.) — the differences show up mostly in interactive features and some scripting syntax.

```bash
cd ~/projects
ls -la
export EDITOR=vim
```

---

## Advanced Tab Completion

```bash
cd Doc<TAB>          # Completes to Documents/, or shows a menu if multiple matches
git chec<TAB><TAB>    # Shows available git subcommands starting with "chec"
```

Zsh's completion system is one of its biggest draws over Bash: it understands context (completing git branch names after `git checkout`, for example) and can show a navigable menu when there are multiple matches, rather than Bash's simpler cycle-through behavior.

---

## Powerful Globbing

```bash
ls **/*.txt              # Recursive glob — match .txt files in any subdirectory
ls *.txt~backup.txt       # Match all .txt files except backup.txt
ls *(m-7)                  # Files modified in the last 7 days (extended glob qualifiers)
```

`**` for recursive globbing and glob qualifiers like `(m-7)` (by modification time) or `(.)` (regular files only) are native Zsh extensions with no direct Bash equivalent, letting you filter file lists by attributes right in the glob pattern itself, without piping through `find`.

---

## Oh My Zsh and Prompt Customization

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

Oh My Zsh is a popular community framework that bundles themes and plugins for Zsh — things like a prompt that shows the current Git branch and status, syntax highlighting for commands as you type them, and auto-suggestions based on history. It's optional (Zsh works fully without it), but it's the reason many developers specifically choose Zsh over Bash for daily interactive use.

---

## Arrays (A Key Difference From Bash)

```zsh
fruits=(apple banana cherry)
echo $fruits[1]          # "apple" — Zsh arrays are 1-indexed by default
echo $fruits[-1]         # "cherry" — negative indices count from the end
```

This 1-based indexing (versus Bash's 0-based `${array[0]}`) is one of the most common sources of bugs when porting a script between the two shells without adjustment.

---

## Configuration Files

Zsh reads `~/.zshrc` for interactive shells — the direct equivalent of Bash's `~/.bashrc`, and the file where Oh My Zsh, aliases, and prompt customizations are typically configured. `~/.zprofile` is the login-shell equivalent of Bash's `~/.bash_profile`.

---

## Spelling Correction

```bash
$ cd Documnets
zsh: correct 'Documnets' to 'Documents' [nyae]?
```

Zsh can detect likely typos in commands or paths and offer to correct them interactively — a small but frequently appreciated feature not present in Bash by default.

---

## Common Gotchas

- Array indexing: 1-based arrays are the single most common Bash→Zsh scripting surprise.
- Word splitting differences: Zsh doesn't split unquoted variables on whitespace by default the way Bash does, which can make some Bash-isms behave differently (usually more predictably, but differently) under Zsh.

---

## Example Walkthrough

```bash
fruits=(apple banana cherry)
for fruit in $fruits; do
    echo "I like $fruit"
done
```

Declares an array and loops over it — syntactically similar to Bash, but worth remembering that indexing and some expansion behaviors differ under the hood.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
EOF

cat > "/home/claude/work/Shells/PowerShell.md" << 'EOF'
[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# PowerShell

PowerShell is a cross-platform shell and scripting language built around **objects** rather than plain text — commands (cmdlets) pass structured .NET objects to each other through pipelines, instead of raw strings the way Unix shells do.

Download: [https://learn.microsoft.com/powershell/](https://learn.microsoft.com/powershell/)

---

## What Is PowerShell?

The biggest conceptual difference between PowerShell and Bash/Zsh is that a PowerShell pipeline passes real, structured objects (with named properties and methods) between commands, not just text. Piping the output of one cmdlet into another means the second cmdlet receives fully structured data it can filter, sort, or select properties from directly — no text parsing with `awk`/`grep`/`cut` required, which is often necessary in Unix-style pipelines to extract specific fields from plain-text output.

---

## Cmdlet Naming Convention

PowerShell commands follow a consistent `Verb-Noun` pattern (`Get-Process`, `Set-Location`, `New-Item`), which makes discovering and guessing command names much easier than the often cryptic, historically-accumulated names of Unix commands (`ls`, `grep`, `awk`).

```powershell
Get-Process                  # List running processes
Get-ChildItem                # List files/folders (aliased as ls or dir)
Set-Location C:\Users         # Change directory (aliased as cd)
New-Item -ItemType File -Name "test.txt"   # Create a new file
Remove-Item file.txt          # Delete a file
```

---

## Core Commands

```powershell
Get-Help Get-Process             # Show detailed help for a cmdlet
Get-Command *service*            # Find cmdlets whose name matches a pattern
Get-Content file.txt             # Print file contents (like cat)
Set-Content file.txt "text"      # Write text to a file
Copy-Item file.txt backup.txt    # Copy a file
Move-Item file.txt folder/       # Move a file
```

---

## Piping Objects, Not Text

```powershell
Get-Process | Where-Object { $_.CPU -gt 100 } | Sort-Object CPU -Descending | Select-Object -First 5
```

This pipeline filters running processes to those using more than 100 CPU seconds, sorts them by CPU usage descending, and shows the top 5 — all operating on structured `Process` objects with real `.CPU` and `.Name` properties, rather than parsing columns out of plain text the way an equivalent `ps aux | sort | head` pipeline in Bash would need to.

---

## Variables and Data Types

```powershell
$name = "Ada"
$numbers = @(1, 2, 3, 4, 5)
$person = @{ Name = "Ada"; Age = 30 }   # A hashtable
Write-Host "Hello, $name"
```

Variables are typed dynamically like most scripting languages, and PowerShell natively supports arrays and hashtables (dictionaries) as first-class objects, rather than the more limited associative-array support found in Bash.

---

## Conditionals and Loops

```powershell
if ($name -eq "Ada") {
    Write-Host "Match!"
}

foreach ($file in Get-ChildItem *.txt) {
    Write-Host $file.Name
}

$i = 0
while ($i -lt 5) {
    Write-Host $i
    $i++
}
```

PowerShell comparison operators are text-based (`-eq`, `-ne`, `-gt`, `-lt`) rather than symbolic, partly because `<` and `>` are already used for redirection in the shell.

---

## Functions

```powershell
function Greet {
    param([string]$Name)
    Write-Host "Hello, $Name!"
}
Greet -Name "Ada"
```

`param()` blocks give PowerShell functions typed, named parameters (with optional defaults and validation), a more structured approach than Bash's positional `$1`, `$2` arguments.

---

## Aliases for Unix Habits

PowerShell ships with built-in aliases mapping familiar Unix commands to their cmdlet equivalents: `ls` → `Get-ChildItem`, `cat` → `Get-Content`, `pwd` → `Get-Location`, `rm` → `Remove-Item`, `cp` → `Copy-Item` — making the transition from a Unix shell smoother, though the underlying objects and full flag sets differ from their Unix namesakes.

---

## Execution Policy

```powershell
Get-ExecutionPolicy
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser
```

Windows restricts running `.ps1` scripts by default as a security measure against malicious scripts being double-clicked accidentally. `Set-ExecutionPolicy` relaxes this for scripts you trust — `RemoteSigned` is a common middle ground, allowing local scripts to run freely while requiring downloaded scripts to be digitally signed.

---

## Common Gotchas

- Cross-platform differences: PowerShell 7+ runs on Linux and macOS too, but some cmdlets (particularly ones tied to Windows-specific features like the registry) aren't available outside Windows.
- Object vs string confusion when interoperating: piping PowerShell output into a traditional text tool (or vice versa) loses the object structure, since text tools only understand plain text.

---

## Example Walkthrough

```powershell
Get-Process | Where-Object { $_.CPU -gt 50 } | Select-Object Name, CPU | Sort-Object CPU -Descending
```

Filters, selects specific properties from, and sorts a list of processes — demonstrating the object-pipeline style that sets PowerShell apart from traditional text-based shells.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
EOF

cat > "/home/claude/work/Shells/Command-Prompt.md" << 'EOF'
[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# Command Prompt (cmd.exe)

Command Prompt is Windows' original command-line shell, dating back to MS-DOS heritage. It's simpler and less powerful than PowerShell, but remains widely used for basic tasks and legacy scripts (`.bat` files).

Download: [https://learn.microsoft.com/windows-server/administration/windows-commands/windows-commands](https://learn.microsoft.com/windows-server/administration/windows-commands/windows-commands)

---

## What Is Command Prompt?

`cmd.exe` interprets commands and batch scripts largely unchanged from the design of MS-DOS, decades ago. It works purely with text, has a much smaller built-in command set than PowerShell, and lacks features like object pipelines, robust error handling, or a real scripting language — it survives today mostly for backward compatibility with older scripts and tools, and because its simplicity makes it fast to launch for one-off tasks.

---

## Core Commands

```
dir                     # List files and folders (equivalent to ls)
cd path                 # Change directory
cd ..                   # Move up one directory
copy file1.txt file2.txt   # Copy a file
move file.txt folder\       # Move a file
del file.txt              # Delete a file
mkdir foldername          # Create a directory
rmdir foldername          # Remove an empty directory
cls                       # Clear the screen
```

---

## Viewing and Managing Files

```
type file.txt              # Print a file's contents (like cat)
more file.txt               # Page through a file's contents
findstr "error" file.txt    # Search for a pattern in a file (like a basic grep)
attrib +r file.txt          # Set the read-only file attribute
```

`findstr` supports basic regular expressions and is the closest built-in equivalent to Unix's `grep`, though considerably less powerful and with different regex syntax.

---

## Environment Variables

```
set                       # List all environment variables
set NAME=Ada               # Set a variable for the current session
echo %NAME%                # Print a variable's value
setx NAME "Ada"             # Set a variable permanently (persists across sessions)
```

Variables are referenced by wrapping the name in percent signs (`%NAME%`), a distinctive syntax compared to Bash's `$NAME` or PowerShell's `$NAME`.

---

## Batch Scripting Basics

```bat
@echo off
set name=World
echo Hello, %name%!

for %%f in (*.txt) do (
    echo Found: %%f
)
```

`@echo off` at the top of a batch file suppresses printing each command before it runs, which is almost always wanted for clean script output. Batch scripting is considerably more limited and quirkier than Bash or PowerShell scripting — modern Windows automation is generally written in PowerShell instead, with `.bat` files mostly surviving for legacy compatibility or extremely simple tasks.

---

## Conditionals and Loops

```bat
if "%name%"=="World" (
    echo Match found
) else (
    echo No match
)

for /L %%i in (1,1,5) do echo %%i
```

`for /L %%i in (start,step,end)` is the batch-file equivalent of a numeric range loop — the syntax is notably more awkward than Bash or PowerShell's loop constructs, one of the main reasons batch scripting fell out of favor for anything beyond simple tasks.

---

## Networking and System Commands

```
ipconfig /all
ping example.com
tasklist                  # List running processes
taskkill /PID 1234 /F      # Force-kill a process by PID
systeminfo                 # Display detailed system configuration
```

---

## Common Gotchas

- Limited scripting power: batch files lack real functions, structured error handling, and rich data types, which is why anything beyond simple automation is usually better done in PowerShell.
- Case and syntax quirks: batch file syntax (especially around quoting, variable expansion timing with `setlocal enabledelayedexpansion`, and loop variables) has many long-standing gotchas that trip up even experienced scripters.

---

## Example Walkthrough

```
@echo off
set folder=C:\Backups
if not exist %folder% mkdir %folder%
copy *.txt %folder%
echo Backup complete.
```

A simple batch script that ensures a backup folder exists, copies text files into it, and confirms completion — representative of the kind of basic automation Command Prompt is still used for today.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)