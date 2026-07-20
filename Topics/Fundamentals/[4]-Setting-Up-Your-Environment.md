# Setting Up Your Environment

Before writing meaningful code, it helps to have a working, comfortable development environment. This section covers the core tools nearly every programmer relies on, regardless of language or specialty.

## 4.1 Installing a Code Editor / IDE

A **code editor** is a text editor tailored for writing code — with features like syntax highlighting, auto-completion, and error checking. An **IDE (Integrated Development Environment)** goes further, bundling an editor together with tools like debuggers, build systems, and project management, all in one application.

Popular options:
- **VS Code** — a free, lightweight, highly extensible editor with strong support for nearly every language via extensions. A common default choice for beginners and professionals alike.
- **JetBrains IDEs** (IntelliJ IDEA, PyCharm, WebStorm, etc.) — full-featured IDEs, often language-specific, with deep tooling (refactoring, debugging, code analysis).
- **Vim / Neovim** — terminal-based, highly efficient once mastered, favored by many experienced developers for speed and keyboard-driven workflows.
- **Sublime Text / Notepad++** — lightweight editors, less commonly used as primary tools today but still around.

For most beginners, VS Code is a reasonable starting point: it's free, well-documented, works across operating systems, and has extensions for virtually every language and workflow.

Key features worth setting up early:
- **Syntax highlighting** — color-coding based on language syntax, making code easier to read
- **Auto-completion / IntelliSense** — suggestions as you type, reducing typos and speeding up writing
- **Linting** — automatic flagging of syntax errors or style issues
- **Integrated terminal** — running commands without leaving the editor
- **Debugger integration** — stepping through code line-by-line to inspect what's happening

## 4.2 Command Line Basics

The **command line** (also called a terminal, shell, or console) is a text-based interface for interacting with your computer — running programs, managing files, and automating tasks — without relying on a graphical interface.

While not strictly required for all programming, comfort with the command line is close to essential in professional environments, since so many tools (Git, package managers, servers, deployment tools) are primarily operated this way.

Core commands to know (Unix-style, used on macOS/Linux, and available on Windows via WSL or Git Bash):

| Command | Purpose |
|---|---|
| `pwd` | Print current directory (where you are) |
| `ls` | List files in the current directory |
| `cd <folder>` | Change directory |
| `mkdir <name>` | Create a new folder |
| `touch <file>` | Create a new empty file |
| `rm <file>` | Delete a file |
| `cp <source> <dest>` | Copy a file |
| `mv <source> <dest>` | Move or rename a file |
| `cat <file>` | Print a file's contents |

Windows has its own native command line (Command Prompt / PowerShell) with somewhat different syntax, though many developers on Windows use **WSL (Windows Subsystem for Linux)** to get a Unix-style environment for consistency with the tools most commonly documented and used industry-wide.

## 4.3 Version Control (Git & GitHub Basics)

**Version control** is a system for tracking changes to code over time — recording who changed what, when, and why, and making it possible to revert to earlier versions or work on changes in parallel without overwriting each other's work.

### Git
**Git** is the dominant version control system used today. It runs locally on your machine and tracks changes through a series of **commits** — saved snapshots of your project at a point in time, each with a message describing what changed.

Core Git concepts:
- **Repository (repo)** — a project being tracked by Git
- **Commit** — a saved snapshot of changes, with a message
- **Branch** — an independent line of development, allowing work on a new feature without affecting the main codebase until it's ready
- **Merge** — combining changes from one branch into another
- **Clone** — copying a remote repository to your local machine

Common commands:
```bash
git init                 # start tracking a new project
git status                # see what's changed
git add <file>            # stage changes for commit
git commit -m "message"   # save a snapshot with a description
git log                   # view commit history
git branch <name>         # create a new branch
git checkout <branch>     # switch to a branch
git merge <branch>        # merge a branch into the current one
```

### GitHub
**GitHub** (along with alternatives like GitLab and Bitbucket) is a cloud platform for hosting Git repositories remotely, enabling collaboration, backup, and sharing of code. Key GitHub concepts:
- **Remote** — a version of the repository hosted online
- **Push / Pull** — sending local commits to GitHub (`push`) or fetching changes from GitHub to your local machine (`pull`)
- **Pull Request (PR)** — a proposal to merge changes from one branch into another, typically reviewed by teammates before merging
- **Issues** — a built-in system for tracking bugs, tasks, and feature requests

Git and GitHub together form the backbone of collaborative software development — nearly every professional team and most open-source projects rely on this workflow.

## 4.4 Package Managers & Dependencies

Most programs don't build everything from scratch — they rely on **dependencies**: external libraries or packages written by others that provide pre-built functionality (parsing JSON, making HTTP requests, handling dates, etc.).

A **package manager** is a tool that automates installing, updating, and removing these dependencies, along with resolving compatibility between them.

Common package managers by language/ecosystem:
| Language | Package Manager | Registry |
|---|---|---|
| JavaScript/Node.js | npm, yarn, pnpm | npmjs.com |
| Python | pip, Poetry | PyPI |
| Rust | Cargo | crates.io |
| Ruby | Bundler/gem | RubyGems |
| Java | Maven, Gradle | Maven Central |

Dependencies are typically declared in a manifest file (e.g., `package.json` for Node.js, `requirements.txt` or `pyproject.toml` for Python), which lists exactly which packages — and often which versions — a project needs. This makes it possible for another developer (or a server) to install the exact same set of dependencies and get a working, reproducible environment.

A related concept is **semantic versioning (semver)** — a common convention of labeling versions as `MAJOR.MINOR.PATCH` (e.g., `2.4.1`), where increases in each number signal the type of change (breaking, new feature, bug fix, respectively).

## 4.5 Environment Variables & Configuration

**Environment variables** are key-value pairs stored outside of your code, at the operating system or shell level, that programs can read at runtime. They're commonly used for:
- **Configuration that changes between environments** — e.g., a database URL that's different for development vs. production
- **Secrets** — API keys, passwords, or tokens that shouldn't be hard-coded directly into source code (especially important if that code is committed to a public repository)
- **Feature flags or settings** — toggling behavior without changing code

Example of setting and reading an environment variable:
```bash
# In a terminal (temporary, current session only)
export API_KEY="your-key-here"
```
```python
# In Python
import os
api_key = os.environ.get("API_KEY")
```

A common practice is to store environment variables for local development in a `.env` file, and to explicitly exclude that file from version control (via `.gitignore`) so secrets are never accidentally committed to a shared repository like GitHub.

### Why This Matters
Getting comfortable with environment variables and configuration early helps avoid a very common beginner mistake: hard-coding sensitive values (like API keys) directly into source code, which is both a security risk and makes it harder to run the same code in different environments (a developer's laptop vs. a live production server) without editing the code itself.