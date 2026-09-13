[Previous](./[4]-System-Settings-And-Customization.md) | [Table of Contents](./[0]-Introduction-to-Windows.md) | [Next](./[6]-Built-In-Tools-And-Software-Ecosystem.md)

*System Features*

# Lesson 5 - The Command Line On Windows

## 5.1 Command Prompt (cmd)

**Command Prompt** (`cmd.exe`) is the oldest command-line interface still available in Windows, tracing its lineage back to MS-DOS. You can open it by searching "cmd" from the Taskbar search box.

```
C:\Users\Jane>dir
C:\Users\Jane>cd Documents
C:\Users\Jane\Documents>mkdir new-project
C:\Users\Jane\Documents>copy report.docx backup.docx
C:\Users\Jane\Documents>del backup.docx
```

Its commands (`dir` instead of `ls`, `copy` instead of `cp`, `del` instead of `rm`) look similar to Unix shells in purpose but different in syntax — a frequent source of confusion for developers moving between Windows and macOS/Linux. Command Prompt is still used today mostly for legacy scripts (`.bat` files) and quick, simple tasks, but it has largely been superseded by PowerShell for anything more advanced.

## 5.2 PowerShell

**PowerShell** is Microsoft's more modern shell and scripting language, built to be far more powerful than Command Prompt. Its biggest structural difference: instead of passing plain text between commands, PowerShell passes structured **objects**.

```powershell
# List running processes, sorted by memory usage
Get-Process | Sort-Object WorkingSet -Descending | Select-Object -First 5

# Create a folder and a file inside it
New-Item -ItemType Directory -Path "NewProject"
New-Item -ItemType File -Path "NewProject\notes.txt"

# Rename every .txt file in a folder to .md
Get-ChildItem *.txt | Rename-Item -NewName { $_.Name -replace '.txt$', '.md' }
```

PowerShell commands follow a consistent **Verb-Noun** naming pattern (`Get-Process`, `New-Item`, `Rename-Item`), which makes them more discoverable and self-descriptive than the short, often cryptic commands in Command Prompt. PowerShell is also cross-platform today (PowerShell 7+ runs on macOS and Linux too), though it's still most deeply integrated into Windows system administration.

## 5.3 Windows Terminal

**Windows Terminal** is a modern terminal application, free from the Microsoft Store, that hosts multiple shells — Command Prompt, PowerShell, and WSL (covered next) — in one tabbed, customizable window.

```
┌───────────────────────────────────────────────┐
│ PowerShell ✕ | Ubuntu (WSL) ✕ | Cmd ✕     +    │
├───────────────────────────────────────────────┤
│ PS C:\Users\Jane>                              │
│                                                 │
└───────────────────────────────────────────────┘
```

Before Windows Terminal, each shell opened in its own separate, fairly bare window with limited customization. Windows Terminal added tabs, panes (splitting one tab into multiple side-by-side shells), custom color schemes, and font ligature support — bringing the terminal experience on Windows much closer to what developers were used to on macOS or Linux. Since Windows 11, it's the default terminal application system-wide.

## 5.4 WSL (Windows Subsystem For Linux)

**WSL (Windows Subsystem for Linux)** lets you run a real Linux environment — Ubuntu, Debian, and others — directly inside Windows, without a traditional virtual machine or dual-boot setup.

```
        Windows 11
┌───────────────────────────────────┐
│  Windows apps    PowerShell        │
│                                     │
│   ┌─────────────────────────┐     │
│   │   WSL2 (Linux Kernel)    │     │
│   │   Ubuntu / Debian / etc. │     │
│   │   bash, apt, systemd     │     │
│   └─────────────────────────┘     │
└───────────────────────────────────┘
```

WSL2 (the current version) runs a real, lightweight Linux kernel, giving near-native performance for Linux-based tools and file operations, while still letting you access Windows files from Linux (and vice versa) and even run Linux GUI apps alongside Windows ones. Installing it is a single command:

```powershell
wsl --install
```

WSL is a major reason Windows has become viable for the kind of development work that previously pushed people toward macOS or Linux — we'll cover this in more depth in Lesson 7.

---

[Previous](./[4]-System-Settings-And-Customization.md) | [Table of Contents](./[0]-Introduction-to-Windows.md) | [Next](./[6]-Built-In-Tools-And-Software-Ecosystem.md)
