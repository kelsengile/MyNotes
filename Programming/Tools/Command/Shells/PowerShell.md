[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

# PowerShell

PowerShell is Microsoft's modern, cross-platform command-line shell and scripting language. Unlike CMD, which passes plain text between commands, PowerShell passes structured **objects**, making it far more powerful for automation and system administration.

Download: [https://learn.microsoft.com/powershell/scripting/install/installing-powershell](https://learn.microsoft.com/powershell/scripting/install/installing-powershell)

---

## What Is PowerShell?

PowerShell's commands are called **cmdlets**, named in a consistent `Verb-Noun` pattern (`Get-Process`, `Set-Location`, `Remove-Item`). Because output is objects rather than plain text, you can pipe the result of one cmdlet directly into another and filter or sort by real properties instead of parsing text.

---

## Everyday Commands

```powershell
Get-Location                          # Equivalent to pwd
Get-ChildItem                         # Equivalent to ls / dir
Set-Location C:\Projects              # Equivalent to cd
Get-Process | Where-Object CPU -gt 50 # Filter processes by CPU usage
Get-Content file.txt                  # Equivalent to cat
```

Many common Unix command names (`ls`, `cat`, `pwd`) work in PowerShell too — they're built-in aliases for the real cmdlets above.

---

## Objects, Not Text

```powershell
Get-Process | Sort-Object CPU -Descending | Select-Object -First 5
```

This sorts running processes by actual CPU usage as a number, then takes the top 5 — something that would require fragile text-parsing in a shell that only passes strings between commands.

---

## Example Walkthrough

```powershell
Get-ChildItem -Path C:\Logs -Filter *.log |
    Where-Object { $_.LastWriteTime -lt (Get-Date).AddDays(-30) } |
    Remove-Item
```

Finds every `.log` file in `C:\Logs` older than 30 days and deletes them — a one-liner that would take a much longer batch script in CMD.

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
