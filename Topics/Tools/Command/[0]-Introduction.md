# Command Prompt (CMD)

Welcome to this lesson series on the Windows Command Prompt (CMD). CMD is a command-line interpreter built into Windows that lets you interact with your computer by typing text commands instead of clicking through menus and windows.

## Why learn CMD?

- **Speed** — many tasks (renaming batches of files, checking network status, killing a frozen process) are faster typed than clicked.
- **Automation** — batch scripts let you chain commands together to automate repetitive work.
- **Troubleshooting** — a huge number of Windows diagnostic and repair tools are CMD-only or CMD-first.
- **Foundations** — understanding CMD makes it much easier to later pick up PowerShell, Linux shells, or scripting languages, since many concepts carry over.

## How this course is organized

Each lesson is a standalone Markdown file. They're numbered so you can work through them in order, but feel free to jump to whichever topic you need. Every lesson includes explanations, example commands, and things to try yourself.

## Table of Contents

| # | Lesson | What you'll learn |
|---|--------|--------------------|
| 1 | [Getting Started with CMD](./[1]-Getting-Started.md) | Opening CMD, the prompt, basic syntax, help, exiting |
| 2 | [Navigating the File System](./[2]-Navigating-the-File-System.md) | `cd`, `dir`, drive letters, paths |
| 3 | [Managing Files and Folders](./[3]-Managing-Files-and-Folders.md) | `mkdir`, `rmdir`, `copy`, `move`, `ren`, `del` |
| 4 | [Viewing and Creating File Content](./[4]-Viewing-and-Creating-File-Content.md) | `type`, `more`, `echo`, redirecting text into files |
| 5 | [Redirection and Piping](./[5]-Redirection-and-Piping.md) | `>`, `>>`, `<`, `\|`, combining commands |
| 6 | [Environment Variables and PATH](./[6]-Environment-Variables-and-PATH.md) | `set`, `%VAR%`, viewing and editing PATH |
| 7 | [System Information Commands](./[7]-System-Information-Commands.md) | `systeminfo`, `whoami`, `ver`, `hostname` |
| 8 | [Process and Task Management](./[8]-Process-and-Task-Management.md) | `tasklist`, `taskkill`, Task Manager from CMD |
| 9 | [Networking Commands](./[9]-Networking-Commands.md) | `ipconfig`, `ping`, `tracert`, `netstat` |
| 10 | [Disk and Drive Management](./[10]-Disk-and-Drive-Management.md) | `chkdsk`, `format`, `diskpart` basics |
| 11 | [User and Permissions Basics](./[11]-User-and-Permissions-Basics.md) | `net user`, `whoami /priv`, running as Administrator |
| 12 | [Batch Scripting Basics](./[12]-Batch-Scripting-Basics.md) | Writing your first `.bat` file, variables, comments |
| 13 | [Batch Scripting: Control Flow](./[13]-Batch-Scripting-Control-Flow.md) | `if`, `for` loops, `goto`, labels |
| 14 | [Tips, Tricks, and Shortcuts](./[14]-Tips-Tricks-and-Shortcuts.md) | Command history, autocomplete, useful keyboard shortcuts |

## Before you begin

- All lessons assume you're using **Windows** (10 or 11). Open CMD by pressing `Win + R`, typing `cmd`, and hitting Enter — or searching "Command Prompt" in the Start menu.
- Some commands (marked in later lessons) require running CMD **as Administrator**. Right-click Command Prompt and choose "Run as administrator."
- Don't worry about memorizing every command. The goal is to get comfortable enough that you can look up details and know what to search for.

