[⬅ Back to README](../../../README.md)

# Command Prompt (CMD)

Welcome to this lesson series on the Windows Command Prompt (CMD). CMD is a command-line interpreter built into Windows that lets you interact with your computer by typing text commands instead of clicking through menus and windows.

## Why learn CMD?

- **Speed** — many tasks (renaming batches of files, checking network status, killing a frozen process) are faster typed than clicked.
- **Automation** — batch scripts let you chain commands together to automate repetitive work.
- **Troubleshooting** — a huge number of Windows diagnostic and repair tools are CMD-only or CMD-first.
- **Foundations** — understanding CMD makes it much easier to later pick up PowerShell, Linux shells, or scripting languages, since many concepts carry over.

## How this course is organized

Each lesson is a standalone Markdown file. They're numbered so you can work through them in order, but feel free to jump to whichever topic you need. Every lesson includes explanations and example commands.

## Table of Contents

1. **[Getting Started](./[1]-Getting-Started.md)**  
   1.1 Opening Command Prompt  
   1.2 Reading the prompt  
   1.3 Your first commands  
   1.4 Getting help  
   1.5 Clearing the screen  
   1.6 Exiting CMD  
2. **[Navigating The File System](./%5B2%5D-Navigating-the-File-System.md)**  
   2.1 Checking where you are  
   2.2 Listing what's in a folder  
   2.3 Changing directories  
   2.4 Switching drives  
   2.5 Path basics  
3. **[Managing Files And Folders](./%5B3%5D-Managing-Files-and-Folders.md)**  
   3.1 Creating folders  
   3.2 Removing folders  
   3.3 Copying files  
   3.4 Copying whole folders  
   3.5 Moving files  
   3.6 Renaming files  
   3.7 Deleting files  
4. **[Viewing And Creating File Content](./%5B4%5D-Viewing-and-Creating-File-Content.md)**  
   4.1 Viewing a text file  
   4.2 Creating a file with text in it  
   4.3 Creating an empty file  
   4.4 Combining several files into one  
   4.5 A quick note on text editors  
5. **[Redirection And Piping](./%5B5%5D-Redirection-and-Piping.md)**  
   5.1 The redirection operators  
   5.2 Examples  
   5.3 Piping between commands  
   5.4 Discarding output  
   5.5 Combining redirection and piping  
6. **[Environment Variables And PATH](./%5B6%5D-Environment-Variables-and-PATH.md)**  
   6.1 What's an environment variable?  
   6.2 Viewing all environment variables  
   6.3 Viewing one variable  
   6.4 Setting a variable for the current session  
   6.5 Setting a variable permanently  
   6.6 What is PATH?  
   6.7 Adding to PATH permanently  
7. **[System Information Commands](./[7]-System-Information-Commands.md)**  
   7.1 Who am I logged in as?  
   7.2 Machine name  
   7.3 Windows version  
   7.4 Full system report  
   7.5 Listing installed hotfixes  
   7.6 Checking disk space  
8. **[Process And Task Management](./%5B8%5D-Process-and-Task-Management.md)**  
   8.1 Listing running processes  
   8.2 Ending a process  
   8.3 Why use CMD instead of Task Manager?  
   8.4 Checking if something is running (for use in scripts)  
9. **[Networking Commands](./[9]-Networking-Commands.md)**  
   9.1 Viewing your network configuration  
   9.2 Renewing your IP address  
   9.3 Flushing the DNS cache  
   9.4 Testing if a host is reachable  
   9.5 Tracing the path to a host  
   9.6 Viewing active connections  
   9.7 Looking up a domain's IP address  
10. **[Disk And Drive Management](./%5B10%5D-Disk-and-Drive-Management.md)**  
    10.1 Checking a disk for errors  
    10.2 Viewing disk space  
    10.3 Formatting a drive  
    10.4 Diskpart — advanced partition management  
11. **[User And Permissions Basics](./%5B11%5D-User-and-Permissions-Basics.md)**  
    11.1 Checking your own permissions  
    11.2 Running CMD as Administrator  
    11.3 Listing user accounts on this machine  
    11.4 Creating a local user account (requires Administrator)  
    11.5 Adding a user to the Administrators group (requires Administrator)  
    11.6 Removing a user account (requires Administrator)  
    11.7 A note on safety  
12. **[Batch Scripting Basics](./[12]-Batch-Scripting-Basics.md)**  
    12.1 Your first batch file  
    12.2 Comments  
    12.3 Variables  
    12.4 Reading input from the user  
    12.5 Using arguments passed to the script  
    12.6 A simple useful example: a backup script  
13. **[Batch Scripting Control Flow](./[13]-Batch-Scripting-Control-Flow.md)**  
    13.1 Conditional logic with if  
    13.2 Chaining commands with && and ||  
    13.3 Loops with for  
    13.4 Labels and goto  
    13.5 Putting it together: a simple menu script  
14. **[Tips Tricks And Shortcuts](./%5B14%5D-Tips-Tricks-and-Shortcuts.md)**  
    14.1 Command history  
    14.2 Autocomplete  
    14.3 Repeating or reusing part of a previous command  
    14.4 Copy and paste  
    14.5 Running multiple commands on one line  
    14.6 Clearing clutter  
    14.7 Useful keyboard shortcuts  
    14.8 Opening CMD in a specific folder quickly  
    14.9 Running a command as a one-off with elevated rights  
    14.10 Where to go from here  

## Before you begin

- All lessons assume you're using **Windows** (10 or 11). Open CMD by pressing `Win + R`, typing `cmd`, and hitting Enter — or searching "Command Prompt" in the Start menu.
- Some commands (marked in later lessons) require running CMD **as Administrator**. Right-click Command Prompt and choose "Run as administrator."
- Don't worry about memorizing every command. The goal is to get comfortable enough that you can look up details and know what to search for.
