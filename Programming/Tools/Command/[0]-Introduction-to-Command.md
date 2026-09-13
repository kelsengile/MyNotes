[⬅ Back to README](../../../README.md)

# Command Prompt (CMD)

Welcome to this lesson series on the Windows Command Prompt (CMD). CMD is a command-line interpreter built into Windows that lets you interact with your computer by typing text commands instead of clicking through menus and windows.

## Why learn CMD?

- **Speed** — many tasks (renaming batches of files, checking network status, killing a frozen process) are faster typed than clicked.
- **Automation** — batch scripts let you chain commands together to automate repetitive work.
- **Troubleshooting** — a huge number of Windows diagnostic and repair tools are CMD-only or CMD-first.
- **Foundations** — understanding CMD makes it much easier to later pick up PowerShell, Linux shells, or scripting languages, since many concepts carry over.

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

## Command-Line Tools:

The lessons above teach the fundamentals of working with command-line interfaces and terminal environments. To see those fundamentals applied to popular, industry-standard command-line tools, continue on to:

**Shells**

* **[Bash](https://www.gnu.org/software/bash/)** — a widely used Unix shell and scripting language commonly found on Linux and macOS systems.

* **[Zsh](https://www.zsh.org/)** — an interactive Unix shell with advanced features for command-line use, scripting, customization, and productivity.

* **[Command Prompt](https://learn.microsoft.com/windows-server/administration/windows-commands/windows-commands)** — the traditional Windows command-line shell used to execute commands, scripts, and system utilities.

* **[PowerShell](https://learn.microsoft.com/powershell/scripting/install/installing-powershell)** — a cross-platform command-line shell and scripting environment designed for automation and system administration.

**Terminal**

* **[Windows Terminal](https://learn.microsoft.com/windows/terminal/)** — a modern terminal application for Windows that supports multiple shells, tabs, profiles, and command-line environments.

* **[GNOME Terminal](https://help.gnome.org/users/gnome-terminal/stable/)** — a terminal emulator commonly used on Linux systems for accessing shells and command-line tools.

* **[Konsole](https://konsole.kde.org/)** — a terminal emulator for KDE-based Linux desktop environments that provides tabs, profiles, and shell access.

**Version Control**

* **[Git](../VersionControl/Git/[0]-Introduction-to-Git.md)** — a distributed version control tool used to track changes, manage code, and collaborate on software projects.

* **[GitHub CLI](https://cli.github.com/)** — GitHub's official command-line interface, used to manage repositories, issues, pull requests, releases, workflows, and other GitHub features directly from the terminal.

**Networking**

* **[SSH](https://www.openssh.com/)** — a secure protocol and command-line tool used to remotely access and manage computers over a network.

* **[cURL](https://curl.se/download.html)** — a command-line tool for transferring data over network protocols such as HTTP, HTTPS, FTP, and SFTP.

* **[Wget](https://www.gnu.org/software/wget/)** — a command-line utility for downloading files and retrieving content from web servers.

* **[ping](https://learn.microsoft.com/windows-server/administration/windows-commands/ping)** — a network diagnostic command used to test connectivity and measure response times between devices.

* **[ipconfig](https://learn.microsoft.com/windows-server/administration/windows-commands/ipconfig)** — a Windows command used to display and manage network interface configuration information.

* **[ip](https://man7.org/linux/man-pages/man8/ip.8.html)** — a Linux command used to configure and inspect network interfaces, addresses, routes, and other networking components.

* **[netstat](https://learn.microsoft.com/windows-server/administration/windows-commands/netstat)** — a command used to display network connections, listening ports, routing information, and network statistics.

* **[tracert](https://learn.microsoft.com/windows-server/administration/windows-commands/tracert)** — a Windows command used to trace the network path packets take to a destination.

* **[traceroute](https://man7.org/linux/man-pages/man8/traceroute.8.html)** — a Unix and Linux utility used to trace the network path between a computer and a destination.

* **[nslookup](https://learn.microsoft.com/windows-server/administration/windows-commands/nslookup)** — a command-line tool used to query DNS records and troubleshoot domain name resolution.

* **[dig](https://bind9.readthedocs.io/en/latest/manpages.html)** — a DNS lookup utility used to query and troubleshoot domain name system records.

**File Management**

* **[cat](https://man7.org/linux/man-pages/man1/cat.1.html)** — a command used to display and concatenate the contents of files.

* **[less](https://man7.org/linux/man-pages/man1/less.1.html)** — a terminal pager used to view large text files and command output interactively.

* **[head](https://man7.org/linux/man-pages/man1/head.1.html)** — a command used to display the beginning of a file or stream.

* **[tail](https://man7.org/linux/man-pages/man1/tail.1.html)** — a command used to display the end of a file or continuously monitor new output.

* **[mkdir](https://man7.org/linux/man-pages/man1/mkdir.1.html)** — a command used to create directories.

* **[cp](https://man7.org/linux/man-pages/man1/cp.1.html)** — a command used to copy files and directories.

* **[mv](https://man7.org/linux/man-pages/man1/mv.1.html)** — a command used to move or rename files and directories.

* **[rm](https://man7.org/linux/man-pages/man1/rm.1.html)** — a command used to remove files and directories.

* **[touch](https://man7.org/linux/man-pages/man1/touch.1.html)** — a command used to create empty files or update file timestamps.

* **[find](https://man7.org/linux/man-pages/man1/find.1.html)** — a command used to search for files and directories based on names, locations, attributes, and other conditions.

* **[tar](https://www.gnu.org/software/tar/)** — a command-line utility used to create, extract, and manage archive files.

* **[tree](https://mama.indstate.edu/users/ice/tree/)** — a command-line utility that displays files and directories in a hierarchical tree structure.

**Text Processing**

* **[grep](https://man7.org/linux/man-pages/man1/grep.1.html)** — a command used to search text for lines matching a specified pattern.

* **[sed](https://www.gnu.org/software/sed/)** — a stream editor used to search, transform, replace, and manipulate text.

* **[awk](https://www.gnu.org/software/gawk/)** — a text-processing language commonly used for filtering, transforming, and analyzing structured text.

* **[sort](https://man7.org/linux/man-pages/man1/sort.1.html)** — a command used to sort lines of text.

* **[uniq](https://man7.org/linux/man-pages/man1/uniq.1.html)** — a command used to detect and filter repeated adjacent lines.

* **[diff](https://man7.org/linux/man-pages/man1/diff.1.html)** — a command used to compare files and identify differences between them.

* **[cut](https://man7.org/linux/man-pages/man1/cut.1.html)** — a command used to extract selected sections or columns from lines of text.

* **[tr](https://man7.org/linux/man-pages/man1/tr.1.html)** — a command used to translate, replace, or remove characters from text streams.

**System Administration**

* **[ps](https://man7.org/linux/man-pages/man1/ps.1.html)** — a command used to display information about currently running processes.

* **[top](https://man7.org/linux/man-pages/man1/top.1.html)** — an interactive command-line utility for monitoring running processes and system resource usage.

* **[kill](https://man7.org/linux/man-pages/man1/kill.1.html)** — a command used to send signals to running processes, including requests to terminate them.

* **[df](https://man7.org/linux/man-pages/man1/df.1.html)** — a command used to display available and used disk space on mounted filesystems.

* **[du](https://man7.org/linux/man-pages/man1/du.1.html)** — a command used to estimate the amount of disk space consumed by files and directories.

* **[chmod](https://man7.org/linux/man-pages/man1/chmod.1.html)** — a command used to change file and directory permissions.

* **[chown](https://man7.org/linux/man-pages/man1/chown.1.html)** — a command used to change file and directory ownership.

* **[systemctl](https://www.freedesktop.org/software/systemd/man/latest/systemctl.html)** — a command used to manage system services and other systemd resources on Linux.

* **[journalctl](https://www.freedesktop.org/software/systemd/man/latest/journalctl.html)** — a command used to view and query systemd journal logs.

**Archives**

* **[tar](https://www.gnu.org/software/tar/)** — a command-line utility used to create, extract, and manage tar archives.

* **[zip](https://infozip.sourceforge.net/)** — a command-line utility used to create and manage ZIP archives.

* **[unzip](https://infozip.sourceforge.net/UnZip.html)** — a command-line utility used to extract files from ZIP archives.

* **[7-Zip](https://www.7-zip.org/)** — a file archiving utility with command-line tools for creating and extracting various archive formats.

**Build Tools**

* **[Make](https://www.gnu.org/software/make/)** — a build automation tool used to compile programs and manage dependencies between source files.

* **[CMake](https://cmake.org/download/)** — a cross-platform build system generator used to configure and manage software compilation.

* **[Ninja](https://ninja-build.org/)** — a small and fast build system designed to execute build instructions efficiently.

* **[Meson](https://mesonbuild.com/)** — a modern build system designed to provide fast and portable project configuration and compilation.