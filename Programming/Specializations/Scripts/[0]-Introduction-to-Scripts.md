[⬅ Back to README](../../../README.md)

# Scripts

Welcome! This is a self-paced course for learning Scripting, the practice of writing small, fast programs to automate tasks, glue systems together, and manage machines from the command line.

---

## What is Scripting?

Scripting lets you:
- Automate repetitive tasks instead of doing them by hand
- Work fluently on the command line and understand how shells process input and output
- Write shell scripts in Bash, with control flow, functions, and file handling
- Process and transform text with pipes, grep, sed, awk, and regular expressions
- Write cross-platform automation scripts in Python
- Automate Windows administration with PowerShell
- Parse arguments, read config files, and work with JSON, CSV & XML from scripts
- Schedule, log, and debug scripts running unattended
- Automate system administration tasks: users, processes, services, backups, and networking
- Package, version, and safely distribute scripts, including as part of CI/CD pipelines

## Table of Contents

**Getting Started**  
    1. **[What is Scripting? Scripting vs Programming](./[1]-What-is-Scripting.md)**  
       1.1 What Is Scripting?  
       1.2 Scripting Vs Programming  
       1.3 Why Learn Scripting?  
    2. **[Choosing a Scripting Language (Bash, Python, PowerShell)](./[2]-Choosing-a-Scripting-Language.md)**  
       2.1 Bash  
       2.2 Python  
       2.3 PowerShell  
       2.4 Choosing Between Them  
    3. **[Setting Up Your Scripting Environment](./[3]-Scripting-Environment-Setup.md)**  
       3.1 Setting Up Bash  
       3.2 Setting Up Python  
       3.3 Setting Up PowerShell  
       3.4 Editors and Tooling  

**Shell Scripting Fundamentals**  
    4. **[Introduction to the Command Line](./[4]-Introduction-to-the-Command-Line.md)**  
       4.1 What Is a Shell?  
       4.2 Basic Navigation  
       4.3 Working With Files  
       4.4 Getting Help  
    5. **[Bash Syntax & Variables](./[5]-Bash-Syntax-and-Variables.md)**  
       5.1 The Shebang Line  
       5.2 Variables  
       5.3 Quoting  
       5.4 Command Substitution and Arithmetic  
    6. **[Control Flow in Bash (Conditionals & Loops)](./[6]-Control-Flow-in-Bash.md)**  
       6.1 If Statements  
       6.2 For Loops  
       6.3 While Loops  
       6.4 Case Statements  
    7. **[Functions in Bash](./[7]-Functions-in-Bash.md)**  
       7.1 Defining and Calling Functions  
       7.2 Arguments and Return Values  
       7.3 Local Variables  
    8. **[Working with Files & Directories in Bash](./[8]-Working-with-Files-and-Directories-in-Bash.md)**  
       8.1 Testing Files and Directories  
       8.2 Reading Files Line by Line  
       8.3 Looping Over Directory Contents  
       8.4 File Permissions  

**Text Processing**  
    9. **[Standard Input, Output & Redirection](./[9]-Standard-Input-Output-and-Redirection.md)**  
       9.1 The Three Standard Streams  
       9.2 Redirection  
       9.3 Here-Documents and Here-Strings  
    10. **[Pipes & Filters](./[10]-Pipes-and-Filters.md)**  
        10.1 What Is a Pipe?  
        10.2 Common Filter Commands  
        10.3 Combining Filters  
    11. **[Text Processing with grep, sed & awk](./[11]-Text-Processing-with-grep-sed-and-awk.md)**  
        11.1 grep — Searching Text  
        11.2 sed — Stream Editor  
        11.3 awk — Pattern Scanning and Processing  
        11.4 Choosing the Right Tool  
    12. **[Regular Expressions](./[12]-Regular-Expressions.md)**  
        12.1 What Is a Regular Expression?  
        12.2 Core Syntax  
        12.3 Using Regex with grep  
        12.4 Practical Tips  

**Automation Basics**  
    13. **[Writing Your First Automation Script](./[13]-Writing-Your-First-Automation-Script.md)**  
        13.1 Planning the Script  
        13.2 A Complete Example  
        13.3 Testing and Iterating  
    14. **[Scheduling Scripts (cron, Task Scheduler)](./[14]-Scheduling-Scripts.md)**  
        14.1 cron (Linux/macOS)  
        14.2 systemd Timers (Linux Alternative)  
        14.3 Task Scheduler (Windows)  
        14.4 Best Practices for Scheduled Scripts  
    15. **[Environment Variables & Configuration Files](./[15]-Environment-Variables-and-Configuration-Files.md)**  
        15.1 What Are Environment Variables?  
        15.2 .env Files  
        15.3 Configuration Files  
    16. **[Error Handling & Exit Codes](./[16]-Error-Handling-and-Exit-Codes.md)**  
        16.1 Exit Codes  
        16.2 Bash Safety Flags  
        16.3 Handling Errors Explicitly  
        16.4 Cleanup with trap  

**Python Scripting**  
    17. **[Python Basics for Scripting](./[17]-Python-Basics-for-Scripting.md)**  
        17.1 Why Python for Scripting  
        17.2 Running a Python Script  
        17.3 Core Syntax Recap  
        17.4 Common Standard Library Modules for Scripting  
    18. **[Working with Files & the Filesystem in Python](./[18]-Working-with-Files-and-the-Filesystem-in-Python.md)**  
        18.1 Reading and Writing Files  
        18.2 pathlib  
        18.3 Common Filesystem Operations  
    19. **[Automating Tasks with Python](./[19]-Automating-Tasks-with-Python.md)**  
        19.1 Running External Commands  
        19.2 A Practical Example: Renaming Files in Bulk  
        19.3 Scheduling Python Scripts  
        19.4 Structuring Larger Automation Scripts  
    20. **[Working with APIs & HTTP Requests in Scripts](./[20]-Working-with-APIs-and-HTTP-Requests-in-Scripts.md)**  
        20.1 Making HTTP Requests  
        20.2 Sending Data (POST Requests)  
        20.3 Authentication  
        20.4 Handling Errors and Rate Limits  

**PowerShell Scripting**  
    21. **[PowerShell Fundamentals](./[21]-PowerShell-Fundamentals.md)**  
        21.1 PowerShell Vs Bash  
        21.2 Cmdlet Naming  
        21.3 Variables and Basic Syntax  
        21.4 The Pipeline  
    22. **[Automating Windows Tasks with PowerShell](./[22]-Automating-Windows-Tasks-with-PowerShell.md)**  
        22.1 Working with Files  
        22.2 Managing Services  
        22.3 Managing Processes  
        22.4 A Complete Example: Cleaning Temp Files  
    23. **[PowerShell Modules & Cmdlets](./[23]-PowerShell-Modules-and-Cmdlets.md)**  
        23.1 What Is a Module?  
        23.2 Discovering Cmdlets  
        23.3 Installing Modules from the Gallery  
        23.4 Writing Your Own Functions  

**Advanced Scripting Concepts**  
    24. **[Argument Parsing & Command-Line Interfaces](./[24]-Argument-Parsing-and-Command-Line-Interfaces.md)**  
        24.1 Positional Arguments in Bash  
        24.2 Parsing Flags in Bash with getopts  
        24.3 Argument Parsing in Python with argparse  
        24.4 Argument Parsing in PowerShell  
    25. **[Working with JSON, CSV & XML in Scripts](./[25]-Working-with-JSON-CSV-and-XML-in-Scripts.md)**  
        25.1 JSON in Python  
        25.2 JSON in Bash with jq  
        25.3 CSV in Python  
        25.4 XML in Python and Structured Data in PowerShell  
    26. **[Logging in Scripts](./[26]-Logging-in-Scripts.md)**  
        26.1 Why Log Instead of Just Printing  
        26.2 Simple Logging in Bash  
        26.3 Logging in Python with the logging Module  
        26.4 Logging in PowerShell  
        26.5 Log Rotation  
    27. **[Debugging Scripts](./[27]-Debugging-Scripts.md)**  
        27.1 Debugging Bash Scripts  
        27.2 Debugging Python Scripts  
        27.3 Debugging PowerShell Scripts  
        27.4 General Debugging Strategy  

**System Administration Scripting**  
    28. **[User & Permission Management Scripts](./[28]-User-and-Permission-Management-Scripts.md)**  
        28.1 Managing Users on Linux  
        28.2 File Permissions and Ownership  
        28.3 A Bulk User-Creation Script  
        28.4 Managing Users on Windows with PowerShell  
    29. **[Process & Service Management Scripts](./[29]-Process-and-Service-Management-Scripts.md)**  
        29.1 Inspecting Processes on Linux  
        29.2 Managing services with systemd  
        29.3 A Health-Check-and-Restart Script  
        29.4 Process and Service Management on Windows  
    30. **[Network Automation Scripts](./[30]-Network-Automation-Scripts.md)**  
        30.1 Basic Network Diagnostics  
        30.2 Checking If a Host or Port Is Reachable  
        30.3 A Simple Uptime Monitor in Python  
        30.4 Network Automation in PowerShell  
    31. **[Backup & Cleanup Scripts](./[31]-Backup-and-Cleanup-Scripts.md)**  
        31.1 A Basic Backup Script  
        31.2 Rotating Old Backups  
        31.3 Cleaning Temporary Files  
        31.4 Backups on Windows with PowerShell  

**Integration & Tooling**  
    32. **[Version Control for Scripts](./[32]-Version-Control-for-Scripts.md)**  
        32.1 Why Version-Control Scripts  
        32.2 Basic Git Workflow  
        32.3 Branching for Changes  
        32.4 Ignoring Generated and Sensitive Files  
    33. **[Packaging & Distributing Scripts](./[33]-Packaging-and-Distributing-Scripts.md)**  
        33.1 Making Bash Scripts Distributable  
        33.2 Packaging Python Scripts  
        33.3 Packaging PowerShell Scripts as Modules  
        33.4 Versioning  
    34. **[Integrating Scripts with CI/CD Pipelines](./[34]-Integrating-Scripts-with-CI-CD-Pipelines.md)**  
        34.1 What Is CI/CD?  
        34.2 A Simple GitHub Actions Example  
        34.3 Designing Scripts for CI/CD  
        34.4 Common Pipeline Script Types  
    35. **[Cross-Platform Scripting Considerations](./[35]-Cross-Platform-Scripting-Considerations.md)**  
        35.1 Line Endings  
        35.2 Path Separators  
        35.3 Case Sensitivity  
        35.4 Choosing a Cross-Platform Approach  

**Best Practices**  
    36. **[Script Security Best Practices](./[36]-Script-Security-Best-Practices.md)**  
        36.1 Never Hardcode Secrets  
        36.2 Validate and Sanitize Input  
        36.3 Principle of Least Privilege  
        36.4 PowerShell Execution Policy  
        36.5 Review Third-Party Scripts Before Running  
    37. **[Writing Maintainable & Readable Scripts](./[37]-Writing-Maintainable-and-Readable-Scripts.md)**  
        37.1 Naming and Structure  
        37.2 Comments and Documentation  
        37.3 Consistent Style  
        37.4 Avoiding Duplication  
    38. **[Testing Scripts](./[38]-Testing-Scripts.md)**  
        38.1 Why Test Scripts?  
        38.2 Manual and Static Testing  
        38.3 Automated Testing in Python  
        38.4 Testing Bash Scripts  
        38.5 Testing in a Safe Environment  
    39. **[Performance Considerations in Scripting](./[39]-Performance-Considerations-in-Scripting.md)**
        39.1 Avoid Unnecessary Subprocesses  
        39.2 Efficient File Reading  
        39.3 Batch Operations Instead of Looping  
        39.4 Measuring Before Optimizing  