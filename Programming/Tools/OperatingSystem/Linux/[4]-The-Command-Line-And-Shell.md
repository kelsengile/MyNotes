[Previous](./[3]-The-Linux-File-System.md) | [Table of Contents](./[0]-Introduction-to-Linux.md) | [Next](./[5]-Package-Management.md)

*The Command Line*

# Lesson 4 - The Command Line And Shell

## 4.1 The Terminal And Common Shells (Bash, Zsh)

The **terminal** (or terminal emulator) is the window you type commands into; the **shell** is the actual program interpreting those commands, running inside the terminal.

```
┌─────────────────────────────────────────┐
│  Terminal Emulator (GNOME Terminal, iTerm) │
│  ┌───────────────────────────────────┐   │
│  │  Shell (bash, zsh, fish)             │   │
│  │  alice@server:~$ _                     │   │
│  └───────────────────────────────────┘   │
└─────────────────────────────────────────┘
```

- **Bash (Bourne Again SHell)** — the long-standing default shell on most Linux distributions and older macOS versions; scripts written for it are the most portable across systems.
- **Zsh (Z Shell)** — the current default on macOS, and a popular choice on Linux, largely backward-compatible with bash but adding richer autocompletion, plugin frameworks (like Oh My Zsh), and better interactive customization.
- **The prompt** — typically shows the username, hostname, and current directory (e.g. `alice@server:~$`), configurable through shell variables like `PS1` in bash.
- **Shell configuration files** — `~/.bashrc` or `~/.zshrc` run automatically for each new interactive shell session, commonly used to set aliases, environment variables, and prompt customizations.

---

## 4.2 Essential Commands (ls, cd, cp, mv, grep...)

A small set of commands covers the overwhelming majority of everyday terminal use:

| Command | Purpose | Example |
|---|---|---|
| `ls` | List directory contents | `ls -la` (all files, long format) |
| `cd` | Change directory | `cd /var/log` |
| `pwd` | Print current directory | `pwd` |
| `cp` | Copy files/directories | `cp -r src/ backup/` |
| `mv` | Move or rename | `mv old.txt new.txt` |
| `rm` | Remove files/directories | `rm -rf build/` |
| `mkdir` | Create a directory | `mkdir -p a/b/c` |
| `cat` | Print a file's contents | `cat notes.txt` |
| `grep` | Search text for a pattern | `grep -r "TODO" src/` |
| `find` | Search for files by criteria | `find . -name "*.log"` |
| `man` | Show a command's manual page | `man grep` |

```
$ grep -r "TODO" src/
src/app.py:14:# TODO: handle empty input
src/utils.py:3:# TODO: refactor this later
```

**A word of caution:** `rm -rf` deletes recursively and forcefully with no confirmation and no recycle bin — always double-check the path (especially when it includes a variable) before pressing Enter.

---

## 4.3 Piping And Redirection

Linux's command-line philosophy favors small, focused tools that can be chained together — **piping** and **redirection** are how that chaining happens.

```
$ ps aux | grep firefox | wc -l
3
```

```
ps aux ──▶ grep firefox ──▶ wc -l
 (list        (filter for       (count
 processes)    matching lines)   lines)
```

- **`|` (pipe)** — sends one command's standard output directly as the next command's standard input, without an intermediate file.
- **`>`** — redirects standard output to a file, overwriting it (`echo "hello" > file.txt`).
- **`>>`** — redirects standard output to a file, appending instead of overwriting (`echo "more" >> file.txt`).
- **`<`** — redirects a file's contents as standard input to a command (`sort < names.txt`).
- **`2>`** — redirects standard error specifically, separate from standard output, useful for sending error messages to a different destination than normal output (`command 2> errors.log`).

This composability is why the Linux command line is often described as a toolbox rather than a single monolithic program — most real tasks are solved by combining several small commands rather than finding one command that does everything.

---

## 4.4 Shell Script Basics

A **shell script** is simply a text file of commands, run in sequence exactly as if typed one after another interactively — automating repetitive command-line work.

```bash
#!/bin/bash
# backup.sh - copies a folder with a timestamped name

SOURCE_DIR="$1"
TIMESTAMP=$(date +%Y%m%d_%H%M%S)
BACKUP_NAME="backup_${TIMESTAMP}"

if [ -d "$SOURCE_DIR" ]; then
    cp -r "$SOURCE_DIR" "$BACKUP_NAME"
    echo "Backed up $SOURCE_DIR to $BACKUP_NAME"
else
    echo "Error: $SOURCE_DIR does not exist"
    exit 1
fi
```

Core building blocks:

- **`#!/bin/bash`** (the "shebang") — the first line, telling the system which interpreter should run the script.
- **`$1`, `$2`, ...** — positional arguments passed to the script on the command line (e.g. `./backup.sh ~/Documents` makes `$1` equal `~/Documents`).
- **Variables** — assigned without spaces around `=` (`NAME="value"`), and read with a `$` prefix (`echo $NAME`).
- **`if`/`[ ]` conditionals** — test conditions like whether a file/directory exists (`-d`, `-f`), whether a string is empty, or whether two values are equal.
- **Making it executable** — `chmod +x backup.sh` (see Lesson 3.2), then run it with `./backup.sh`.

Shell scripts are the connective tissue behind much of Linux system administration — deployment scripts, cron jobs (scheduled tasks), and startup routines are all commonly just bash scripts calling the same essential commands covered in 4.2.

[Previous](./[3]-The-Linux-File-System.md) | [Table of Contents](./[0]-Introduction-to-Linux.md) | [Next](./[5]-Package-Management.md)
