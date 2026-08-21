[Previous](./[1]-What-is-Scripting.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[3]-Scripting-Environment-Setup.md)

*Getting Started*

# Lesson 2 - Choosing A Scripting Language (Bash, Python, PowerShell)

## 2.1 Bash

Bash is the default shell and scripting language on most Linux and macOS systems. It excels at:
- Chaining and orchestrating other command-line programs
- Quick one-off automation directly in the terminal
- File and process manipulation on Unix-like systems

It's less suited to complex data structures, math, or large programs — syntax gets awkward quickly beyond a few hundred lines.

---

## 2.2 Python

Python is a general-purpose language that is also excellent for scripting. It excels at:
- Cross-platform scripts (the same script can run on Linux, macOS, and Windows)
- Working with structured data (JSON, CSV, XML) and APIs
- Larger or more complex automation where readability and maintainability matter

Python requires an interpreter to be installed, and its standard library plus package ecosystem (`pip`) make it the most versatile choice for automation beyond simple shell tasks.

---

## 2.3 PowerShell

PowerShell is Microsoft's scripting language and shell, built into Windows and also available cross-platform (PowerShell 7+). It excels at:
- Administering Windows systems (users, services, the registry, Active Directory)
- Working with structured **objects** instead of plain text (a key difference from Bash)
- Integrating with the .NET ecosystem

---

## 2.4 Choosing Between Them

A simple rule of thumb:

| Situation | Best fit |
|---|---|
| Automating Linux/macOS command-line tasks | Bash |
| Cross-platform automation, APIs, data processing | Python |
| Automating Windows administration | PowerShell |

Many real-world workflows combine more than one — for example, a Bash script that calls a Python script, scheduled by cron. This course covers all three so you can pick the right tool for the job.

---

[Previous](./[1]-What-is-Scripting.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[3]-Scripting-Environment-Setup.md)
