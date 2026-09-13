[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# PowerShell

PowerShell is Microsoft's modern command-line shell and scripting language, built on .NET. Unlike traditional shells that pass plain text between commands, PowerShell passes structured **objects** — a design choice that fundamentally changes how commands are written and combined.

Reference: [https://learn.microsoft.com/en-us/powershell/](https://learn.microsoft.com/en-us/powershell/)

---

## What Is PowerShell?

First released in 2006 (originally codenamed "Monad"), PowerShell was designed from the ground up to replace both Command Prompt/batch scripting and the ad-hoc WMI/VBScript tooling Windows administrators had relied on. Its defining idea: every command (called a **cmdlet**, pronounced "command-let") outputs full .NET objects — not formatted text — so the next command in a pipeline can access an object's actual properties and methods directly, rather than needing to parse text output the way Unix pipelines traditionally do.

Since PowerShell 6 (renamed **PowerShell Core**, later just "PowerShell 7+"), it's been open-source and cross-platform, running natively on Windows, Linux, and macOS — a major departure from the Windows-only "Windows PowerShell" versions (5.1 and earlier) still built into Windows itself.

---

## Cmdlet Naming: Verb-Noun

Nearly every built-in command follows a strict `Verb-Noun` naming pattern, using a standardized, limited set of approved verbs (`Get`, `Set`, `New`, `Remove`, `Start`, `Stop`, `Add`, etc.), which makes the whole command surface far more predictable than Unix's historically inconsistent, terse command names.

```powershell
Get-Process                    # List running processes
Get-Process -Name chrome       # Filter by name
Stop-Process -Name chrome      # Kill a process
Get-Service                    # List Windows services
Get-ChildItem                  # List files/folders (aliased as ls or dir)
Set-Location C:\Projects       # Change directory (aliased as cd)
Copy-Item file.txt backup.txt  # Copy a file
Remove-Item file.txt           # Delete a file
New-Item -ItemType Directory foldername  # Create a directory
Get-Content file.txt           # Print a file's contents (like cat)
Select-String "error" log.txt  # Search text (like grep)
Get-Help Get-Process -Full     # Full documentation for any cmdlet
```

## Working with Objects, Not Text

```powershell
Get-Process | Sort-Object CPU -Descending | Select-Object -First 5 Name, CPU
```

This pipeline sorts running processes by actual CPU-usage *property values* (not by parsing text columns the way Unix would need `sort -k3`), then selects just two named properties from the top five — direct, structured access that's far less fragile than text-column parsing.

```powershell
Get-ChildItem *.log | Where-Object { $_.Length -gt 1MB } | Remove-Item
```

Filters files by their actual `Length` property (a real number, not a string to parse) before deleting the matches — a pattern common across almost every PowerShell pipeline.

---

## Common Aliases (Unix and cmd Habits Still Work)

| Alias | Real cmdlet |
|---|---|
| `ls`, `dir` | `Get-ChildItem` |
| `cd` | `Set-Location` |
| `pwd` | `Get-Location` |
| `cat`, `type` | `Get-Content` |
| `rm`, `del` | `Remove-Item` |
| `cp`, `copy` | `Copy-Item` |
| `mv`, `move` | `Move-Item` |
| `ps` | `Get-Process` |
| `kill` | `Stop-Process` |
| `clear`, `cls` | `Clear-Host` |
| `echo` | `Write-Output` |

These aliases exist specifically to ease the transition for people coming from Unix shells or Command Prompt, while the "real" underlying commands remain the full `Verb-Noun` cmdlets.

---

## Scripting: .ps1 Files, Functions, and Modules

```powershell
function Get-DiskSpaceReport {
    param([string]$Path = "C:\")
    Get-PSDrive -PSProvider FileSystem | Where-Object { $_.Root -eq $Path }
}
```

PowerShell scripts (`.ps1`) support real functions with typed parameters, error handling (`try`/`catch`), classes (since PowerShell 5), and modules — reusable, shareable packages of cmdlets, many of which are published to the **PowerShell Gallery** (`Install-Module`) covering everything from Azure and AWS management to Active Directory administration.

---

## Beyond the Shell: What Else PowerShell Does

- **Remote administration (PowerShell Remoting / WinRM)**: `Invoke-Command -ComputerName Server01 -ScriptBlock { Get-Service }` runs commands on remote machines, forming the backbone of most enterprise Windows fleet management and much of Azure automation.
- **Managing Windows itself**: administering Active Directory, IIS, Exchange, SQL Server, Azure, and Microsoft 365 are all commonly done through dedicated PowerShell modules rather than GUI tools, especially at scale.
- **Desired State Configuration (DSC)**: a PowerShell-based framework for declaratively defining and enforcing a machine's configuration, conceptually similar to tools like Ansible or Puppet.
- **.NET integration**: PowerShell can directly instantiate and call into any .NET class (`[System.IO.File]::ReadAllText(...)`), giving scripts access to the full .NET framework/Core class library when cmdlets aren't enough.
- **Cross-platform automation**: PowerShell 7+ runs the same scripts on Linux and macOS, making it a genuine option for cross-platform DevOps tooling, not just a Windows-only tool anymore.

---

## Execution Policy and Security

By default, Windows restricts running unsigned `.ps1` scripts via an **execution policy** (`Get-ExecutionPolicy`, `Set-ExecutionPolicy`), a safeguard against accidentally or maliciously running arbitrary downloaded scripts — a concept with no direct equivalent in Unix shells, where script execution is generally only gated by file permission bits.

---

## Related Tools

- **Windows Terminal** — the modern terminal host that runs PowerShell (and cmd, and WSL) in a tabbed interface.
- **Command Prompt** — the older, plain-text Windows shell PowerShell was designed to eventually replace.
- **Bash/Zsh** — Unix shells with a comparable scripting role, but a fundamentally text-based (not object-based) pipeline model.
- **Azure CLI / AWS CLI** — cloud-provider command-line tools often used alongside or wrapped by PowerShell scripts.

---

## Example Walkthrough

```powershell
Get-ChildItem -Path C:\Logs -Recurse -Filter *.log |
  Where-Object { $_.LastWriteTime -lt (Get-Date).AddDays(-30) } |
  Remove-Item -WhatIf
```

Finds every `.log` file under a directory tree older than 30 days and previews what would be deleted (`-WhatIf` simulates the action without actually removing anything) — a safe, idiomatic PowerShell pattern for testing a destructive script before running it for real by dropping the `-WhatIf` flag.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)