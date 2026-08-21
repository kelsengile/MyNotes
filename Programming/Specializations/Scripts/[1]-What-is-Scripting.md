[Previous](./[0]-Introduction-to-Scripts.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[2]-Choosing-a-Scripting-Language.md)

*Getting Started*

# Lesson 1 - What Is Scripting? Scripting Vs Programming

## 1.1 What Is Scripting?

A **script** is a small program, usually written in an interpreted language, that automates a task: running a sequence of commands, transforming files, calling APIs, or managing a system. Scripts are typically run directly by an interpreter (Bash, Python, PowerShell) rather than compiled into a standalone binary first.

Common uses for scripts include:
- Automating repetitive command-line tasks (renaming files, backing up folders)
- "Gluing" other programs together (piping the output of one tool into another)
- System administration (creating users, restarting services, rotating logs)
- Build and deployment automation (CI/CD pipelines)

---

## 1.2 Scripting Vs Programming

The terms overlap, but there are useful distinctions:

| Scripting | Programming (general) |
|---|---|
| Usually interpreted, not compiled | Often compiled (C, Go, Rust) or JIT-compiled |
| Short-lived, task-focused programs | Long-running applications, libraries, systems |
| Glues existing tools/programs together | Builds new tools/programs from scratch |
| Fast to write and iterate on | May require more upfront design |

In practice, languages like Python and JavaScript are used for both scripting *and* full application development — the distinction is more about **intent and scale** than the language itself. A 20-line script that backs up a folder is scripting; a web framework serving millions of users is programming, even if both are written in Python.

---

## 1.3 Why Learn Scripting?

Scripting skills are valuable because they let you:
- Save time by automating anything you do more than once
- Understand and control the systems you work on (servers, CI pipelines, local machines)
- Build small tools quickly without the overhead of a full application
- Work effectively across Linux, macOS, and Windows environments

This course starts with the command line and Bash, moves into text processing, then automation with Python and PowerShell, and finishes with system administration and best practices.

---

[Previous](./[0]-Introduction-to-Scripts.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[2]-Choosing-a-Scripting-Language.md)
