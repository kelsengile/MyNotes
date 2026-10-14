[⬅ Back to README](../../../README.md)

<div align="center">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/bash/bash-original.svg" width="50"/><br>
  <h1 style="margin-top: 0;">Command</h1>
</div>

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

---

## Command-Line Tools:

The lessons above teach the fundamentals of working with command-line interfaces and terminal environments. To see those fundamentals applied to popular, industry-standard command-line tools, continue on to:

<table align="center" width="100%" style="margin-left:auto;margin-right:auto;table-layout:fixed;">
<tr><th colspan="2" align="center">Shells</th></tr>
<tr><th align="center" width="25%">Tool</th><th align="center" width="75%">Description</th></tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Shells/Bash.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/bash/bash-original.svg" width="40" title="Bash"/><br>Bash</a></td>
<td align="left" width="75%">A widely used Unix shell and scripting language commonly found on Linux and macOS systems.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Shells/Zsh.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/zsh/zsh-original.svg" width="40" title="Zsh"/><br>Zsh</a></td>
<td align="left" width="75%">An interactive Unix shell with advanced features for command-line use, scripting, customization, and productivity.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Shells/Command-Prompt.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/windows8/windows8-original.svg" width="40" title="Command Prompt"/><br>Command Prompt</a></td>
<td align="left" width="75%">The traditional Windows command-line shell used to execute commands, scripts, and system utilities.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Shells/PowerShell.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/powershell/powershell-original.svg" width="40" title="PowerShell"/><br>PowerShell</a></td>
<td align="left" width="75%">A cross-platform command-line shell and scripting environment designed for automation and system administration.</td>
</tr>
</table>

<table align="center" width="100%" style="margin-left:auto;margin-right:auto;table-layout:fixed;">
<tr><th colspan="2" align="center">Terminal</th></tr>
<tr><th align="center" width="25%">Tool</th><th align="center" width="75%">Description</th></tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Terminals/Windows-Terminal.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/windows8/windows8-original.svg" width="40" title="Windows Terminal"/><br>Windows Terminal</a></td>
<td align="left" width="75%">A modern terminal application for Windows that supports multiple shells, tabs, profiles, and command-line environments.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Terminals/GNOME-Terminal.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/linux/linux-original.svg" width="40" title="GNOME Terminal"/><br>GNOME Terminal</a></td>
<td align="left" width="75%">A terminal emulator commonly used on Linux systems for accessing shells and command-line tools.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Terminals/Konsole.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/linux/linux-original.svg" width="40" title="Konsole"/><br>Konsole</a></td>
<td align="left" width="75%">A terminal emulator for KDE-based Linux desktop environments that provides tabs, profiles, and shell access.</td>
</tr>
</table>

<table align="center" width="100%" style="margin-left:auto;margin-right:auto;table-layout:fixed;">
<tr><th colspan="2" align="center">Version Control</th></tr>
<tr><th align="center" width="25%">Tool</th><th align="center" width="75%">Description</th></tr>
<tr>
<td align="center" width="25%" height="100"><a href="../VersionControl/Git/%5B0%5D-Introduction-to-Git.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/git/git-original.svg" width="40" title="Git"/><br>Git</a></td>
<td align="left" width="75%">A distributed version control tool used to track changes, manage code, and collaborate on software projects.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="https://cli.github.com/"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/github/github-original.svg" width="40" title="GitHub CLI"/><br>GitHub CLI</a></td>
<td align="left" width="75%">GitHub's official command-line interface, used to manage repositories, issues, pull requests, releases, workflows, and other GitHub features directly from the terminal.</td>
</tr>
</table>

<table align="center" width="100%" style="margin-left:auto;margin-right:auto;table-layout:fixed;">
<tr><th colspan="2" align="center">Networking</th></tr>
<tr><th align="center" width="25%">Tool</th><th align="center" width="75%">Description</th></tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Networking/SSH.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f510.svg" width="40" title="SSH"/><br>SSH</a></td>
<td align="left" width="75%">A secure protocol and command-line tool used to remotely access and manage computers over a network.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Networking/cURL.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f310.svg" width="40" title="cURL"/><br>cURL</a></td>
<td align="left" width="75%">A command-line tool for transferring data over network protocols such as HTTP, HTTPS, FTP, and SFTP.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Networking/Wget.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f4e5.svg" width="40" title="Wget"/><br>Wget</a></td>
<td align="left" width="75%">A command-line utility for downloading files and retrieving content from web servers.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Networking/ping.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f4e1.svg" width="40" title="ping"/><br>ping</a></td>
<td align="left" width="75%">A network diagnostic command used to test connectivity and measure response times between devices.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Networking/ipconfig.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/windows8/windows8-original.svg" width="40" title="ipconfig"/><br>ipconfig</a></td>
<td align="left" width="75%">A Windows command used to display and manage network interface configuration information.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Networking/ip.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/linux/linux-original.svg" width="40" title="ip"/><br>ip</a></td>
<td align="left" width="75%">A Linux command used to configure and inspect network interfaces, addresses, routes, and other networking components.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Networking/netstat.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f50c.svg" width="40" title="netstat"/><br>netstat</a></td>
<td align="left" width="75%">A command used to display network connections, listening ports, routing information, and network statistics.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Networking/tracert.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/windows8/windows8-original.svg" width="40" title="tracert"/><br>tracert</a></td>
<td align="left" width="75%">A Windows command used to trace the network path packets take to a destination.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Networking/traceroute.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f9ed.svg" width="40" title="traceroute"/><br>traceroute</a></td>
<td align="left" width="75%">A Unix and Linux utility used to trace the network path between a computer and a destination.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Networking/nslookup.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f4d2.svg" width="40" title="nslookup"/><br>nslookup</a></td>
<td align="left" width="75%">A command-line tool used to query DNS records and troubleshoot domain name resolution.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Networking/dig.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/26cf.svg" width="40" title="dig"/><br>dig</a></td>
<td align="left" width="75%">A DNS lookup utility used to query and troubleshoot domain name system records.</td>
</tr>
</table>

<table align="center" width="100%" style="margin-left:auto;margin-right:auto;table-layout:fixed;">
<tr><th colspan="2" align="center">File Management</th></tr>
<tr><th align="center" width="25%">Tool</th><th align="center" width="75%">Description</th></tr>
<tr>
<td align="center" width="25%" height="100"><a href="./File%20Management/cat.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f431.svg" width="40" title="cat"/><br>cat</a></td>
<td align="left" width="75%">A command used to display and concatenate the contents of files.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./File%20Management/less.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f4c4.svg" width="40" title="less"/><br>less</a></td>
<td align="left" width="75%">A terminal pager used to view large text files and command output interactively.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./File%20Management/head.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/2b06.svg" width="40" title="head"/><br>head</a></td>
<td align="left" width="75%">A command used to display the beginning of a file or stream.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./File%20Management/tail.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/2b07.svg" width="40" title="tail"/><br>tail</a></td>
<td align="left" width="75%">A command used to display the end of a file or continuously monitor new output.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./File%20Management/mkdir.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f4c1.svg" width="40" title="mkdir"/><br>mkdir</a></td>
<td align="left" width="75%">A command used to create directories.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./File%20Management/cp.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f4cb.svg" width="40" title="cp"/><br>cp</a></td>
<td align="left" width="75%">A command used to copy files and directories.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./File%20Management/mv.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/27a1.svg" width="40" title="mv"/><br>mv</a></td>
<td align="left" width="75%">A command used to move or rename files and directories.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./File%20Management/rm.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f5d1.svg" width="40" title="rm"/><br>rm</a></td>
<td align="left" width="75%">A command used to remove files and directories.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./File%20Management/touch.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f446.svg" width="40" title="touch"/><br>touch</a></td>
<td align="left" width="75%">A command used to create empty files or update file timestamps.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./File%20Management/find.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f50e.svg" width="40" title="find"/><br>find</a></td>
<td align="left" width="75%">A command used to search for files and directories based on names, locations, attributes, and other conditions.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./File%20Management/tar.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f4e6.svg" width="40" title="tar"/><br>tar</a></td>
<td align="left" width="75%">A command-line utility used to create, extract, and manage archive files.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./File%20Management/tree.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f332.svg" width="40" title="tree"/><br>tree</a></td>
<td align="left" width="75%">A command-line utility that displays files and directories in a hierarchical tree structure.</td>
</tr>
</table>

<table align="center" width="100%" style="margin-left:auto;margin-right:auto;table-layout:fixed;">
<tr><th colspan="2" align="center">Text Processing</th></tr>
<tr><th align="center" width="25%">Tool</th><th align="center" width="75%">Description</th></tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Text%20Processing/grep.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f50d.svg" width="40" title="grep"/><br>grep</a></td>
<td align="left" width="75%">A command used to search text for lines matching a specified pattern.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Text%20Processing/sed.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/270f.svg" width="40" title="sed"/><br>sed</a></td>
<td align="left" width="75%">A stream editor used to search, transform, replace, and manipulate text.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Text%20Processing/awk.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f9ee.svg" width="40" title="awk"/><br>awk</a></td>
<td align="left" width="75%">A text-processing language commonly used for filtering, transforming, and analyzing structured text.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Text%20Processing/sort.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f522.svg" width="40" title="sort"/><br>sort</a></td>
<td align="left" width="75%">A command used to sort lines of text.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Text%20Processing/uniq.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/2728.svg" width="40" title="uniq"/><br>uniq</a></td>
<td align="left" width="75%">A command used to detect and filter repeated adjacent lines.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Text%20Processing/diff.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/2696.svg" width="40" title="diff"/><br>diff</a></td>
<td align="left" width="75%">A command used to compare files and identify differences between them.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Text%20Processing/cut.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/2702.svg" width="40" title="cut"/><br>cut</a></td>
<td align="left" width="75%">A command used to extract selected sections or columns from lines of text.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Text%20Processing/tr.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f501.svg" width="40" title="tr"/><br>tr</a></td>
<td align="left" width="75%">A command used to translate, replace, or remove characters from text streams.</td>
</tr>
</table>

<table align="center" width="100%" style="margin-left:auto;margin-right:auto;table-layout:fixed;">
<tr><th colspan="2" align="center">System Administration</th></tr>
<tr><th align="center" width="25%">Tool</th><th align="center" width="75%">Description</th></tr>
<tr>
<td align="center" width="25%" height="100"><a href="./System%20Administration/ps.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f9fe.svg" width="40" title="ps"/><br>ps</a></td>
<td align="left" width="75%">A command used to display information about currently running processes.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./System%20Administration/top.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f4c8.svg" width="40" title="top"/><br>top</a></td>
<td align="left" width="75%">An interactive command-line utility for monitoring running processes and system resource usage.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./System%20Administration/kill.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f480.svg" width="40" title="kill"/><br>kill</a></td>
<td align="left" width="75%">A command used to send signals to running processes, including requests to terminate them.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./System%20Administration/df.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f4be.svg" width="40" title="df"/><br>df</a></td>
<td align="left" width="75%">A command used to display available and used disk space on mounted filesystems.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./System%20Administration/du.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f4cf.svg" width="40" title="du"/><br>du</a></td>
<td align="left" width="75%">A command used to estimate the amount of disk space consumed by files and directories.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./System%20Administration/chmod.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f512.svg" width="40" title="chmod"/><br>chmod</a></td>
<td align="left" width="75%">A command used to change file and directory permissions.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./System%20Administration/chown.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f511.svg" width="40" title="chown"/><br>chown</a></td>
<td align="left" width="75%">A command used to change file and directory ownership.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./System%20Administration/systemctl.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/2699.svg" width="40" title="systemctl"/><br>systemctl</a></td>
<td align="left" width="75%">A command used to manage system services and other systemd resources on Linux.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./System%20Administration/journalctl.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f4d3.svg" width="40" title="journalctl"/><br>journalctl</a></td>
<td align="left" width="75%">A command used to view and query systemd journal logs.</td>
</tr>
</table>

<table align="center" width="100%" style="margin-left:auto;margin-right:auto;table-layout:fixed;">
<tr><th colspan="2" align="center">Archives</th></tr>
<tr><th align="center" width="25%">Tool</th><th align="center" width="75%">Description</th></tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Archives/tar.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f4e6.svg" width="40" title="tar"/><br>tar</a></td>
<td align="left" width="75%">A command-line utility used to create, extract, and manage tar archives.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Archives/zip.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f5dc.svg" width="40" title="zip"/><br>zip</a></td>
<td align="left" width="75%">A command-line utility used to create and manage ZIP archives.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Archives/unzip.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f4c2.svg" width="40" title="unzip"/><br>unzip</a></td>
<td align="left" width="75%">A command-line utility used to extract files from ZIP archives.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Archives/7-Zip.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f5c3.svg" width="40" title="7-Zip"/><br>7-Zip</a></td>
<td align="left" width="75%">A file archiving utility with command-line tools for creating and extracting various archive formats.</td>
</tr>
</table>

<table align="center" width="100%" style="margin-left:auto;margin-right:auto;table-layout:fixed;">
<tr><th colspan="2" align="center">Build Tools</th></tr>
<tr><th align="center" width="25%">Tool</th><th align="center" width="75%">Description</th></tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Build%20Tools/Make.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f528.svg" width="40" title="Make"/><br>Make</a></td>
<td align="left" width="75%">A build automation tool used to compile programs and manage dependencies between source files.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Build%20Tools/CMake.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/cmake/cmake-original.svg" width="40" title="CMake"/><br>CMake</a></td>
<td align="left" width="75%">A cross-platform build system generator used to configure and manage software compilation.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Build%20Tools/Ninja.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f977.svg" width="40" title="Ninja"/><br>Ninja</a></td>
<td align="left" width="75%">A small and fast build system designed to execute build instructions efficiently.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Build%20Tools/Meson.md"><img src="https://cdn.jsdelivr.net/gh/jdecked/twemoji@16.0.1/assets/svg/1f3d7.svg" width="40" title="Meson"/><br>Meson</a></td>
<td align="left" width="75%">A modern build system designed to provide fast and portable project configuration and compilation.</td>
</tr>
</table>

<table align="center" width="100%" style="margin-left:auto;margin-right:auto;table-layout:fixed;">
<tr><th colspan="2" align="center">Terminal Editors</th></tr>
<tr><th align="center" width="25%">Tool</th><th align="center" width="75%">Description</th></tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Terminal%20Editors/Vim.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/vim/vim-original.svg" width="40" title="Vim"/><br>Vim</a></td>
<td align="left" width="75%">A powerful, highly configurable terminal-based text editor commonly used for editing code and configuration files.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Terminal%20Editors/Nano.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/nano/nano-original.svg" width="40" title="Nano"/><br>Nano</a></td>
<td align="left" width="75%">A simple and user-friendly terminal text editor designed for quickly editing files from the command line.</td>
</tr>
<tr>
<td align="center" width="25%" height="100"><a href="./Terminal%20Editors/tmux.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/tmux/tmux-original.svg" width="40" title="tmux"/><br>tmux</a></td>
<td align="left" width="75%">A terminal multiplexer that allows users to run multiple terminal sessions, windows, and panes within a single terminal.</td>
</tr>
</table>