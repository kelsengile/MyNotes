[Previous](./[6]-Built-In-Tools-And-Software-Ecosystem.md) | [Table of Contents](./[0]-Introduction-to-Windows.md)

*Using Windows For Development*

# Lesson 7 - Using Windows For Development

## 7.1 Setting Up A Dev Environment On Windows

A typical developer setup on Windows layers several tools on top of the base system covered in earlier lessons:

| Layer | Example Tools |
|---|---|
| Code editor / IDE | Visual Studio Code, Visual Studio, JetBrains IDEs |
| Package manager | winget (built-in), Chocolatey, Scoop |
| Version control | Git, connected to GitHub/GitLab |
| Language runtimes | Node.js, Python, .NET, Java — often via installers or version managers |
| Terminal | Windows Terminal (Lesson 5), hosting PowerShell and/or WSL |
| Containers/local servers | Docker Desktop, local databases (SQL Server, PostgreSQL) |

**winget**, Windows' built-in package manager, works similarly to Homebrew on macOS:

```powershell
winget install Git.Git
winget install Microsoft.VisualStudioCode
winget upgrade --all
```

Unlike a Mac's relatively uniform hardware, Windows developers often need to account for a wider variety of GPU drivers, CPU architectures, and peripheral configurations, since the same version of Windows might be running on dramatically different hardware from machine to machine.

## 7.2 WSL2 For Linux-Based Development

Building on Lesson 5, **WSL2** has become the standard way Windows developers run Linux-native tools without leaving their main machine. A common workflow looks like this:

```
wsl --install -d Ubuntu     # install a Linux distro
wsl                         # enter the Linux environment
sudo apt update && sudo apt upgrade
sudo apt install python3 nodejs npm git
```

Once inside WSL2, commands, package managers (`apt`, `pip`), and file permissions behave exactly as they would on a real Ubuntu server — closing much of the gap that used to exist between developing on Windows and deploying to Linux-based production servers. Files can be accessed across the Windows/Linux boundary (`\\wsl$\Ubuntu\home\jane` from Windows, or `/mnt/c/Users/Jane` from Linux), though for best performance, project files are usually kept entirely within the Linux filesystem rather than the Windows one.

## 7.3 The Windows Terminal + VS Code Workflow

**Visual Studio Code**, Microsoft's free, cross-platform code editor, integrates tightly with both Windows Terminal and WSL through an extension called **Remote - WSL**:

```
┌─────────────────────────────────────────────┐
│  VS Code Window                               │
│  ┌─────────────┐   ┌───────────────────────┐│
│  │  Editor      │   │  Integrated Terminal   ││
│  │  (editing    │   │  (running inside       ││
│  │  files       │   │   WSL2 Ubuntu)         ││
│  │  inside WSL) │   │                         ││
│  └─────────────┘   └───────────────────────┘│
└─────────────────────────────────────────────┘
```

With this setup, VS Code's interface runs natively on Windows while the actual file system, terminal, and language tools it's operating on run inside WSL2 — giving a Linux-based development experience with a fully native, responsive Windows-based editor UI. This combination (Windows Terminal + VS Code + WSL2) is one of the most common modern setups for web and backend developers who prefer Windows hardware but need Linux-compatible tooling.

## 7.4 Why Many Studios And Enterprises Standardize On Windows

Pulling together everything from this Topic, several reasons consistently explain why so many companies — especially in gaming and enterprise software — standardize on Windows:

- **Hardware flexibility and cost** — companies can choose from a huge range of manufacturers and price points rather than being locked into one vendor's lineup.
- **PC gaming dominance** — game studios build and test primarily on Windows, since it's where the overwhelming majority of PC gamers are.
- **Enterprise IT tooling** — Active Directory, Group Policy, and Microsoft 365 give large organizations mature, centralized ways to manage thousands of machines at once.
- **.NET and Visual Studio** — a mature, first-party development platform for enterprise and Windows-native software.
- **WSL2 closing the Linux gap** — teams that need Linux-based development workflows no longer have to give up Windows hardware to get them.

As with macOS, this doesn't make Windows the universally "correct" choice — the decision usually comes down to what a team's software targets (Windows-only enterprise tools vs. Apple platform apps vs. general Linux/web development) and what hardware and IT ecosystem a given organization already has in place.

---

[Previous](./[6]-Built-In-Tools-And-Software-Ecosystem.md) | [Table of Contents](./[0]-Introduction-to-Windows.md)
