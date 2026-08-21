[Previous](./[20]-Working-with-APIs-and-HTTP-Requests-in-Scripts.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[22]-Automating-Windows-Tasks-with-PowerShell.md)

*PowerShell Scripting*

# Lesson 21 - PowerShell Fundamentals

## 21.1 PowerShell Vs Bash

The biggest difference from Bash: PowerShell commands (**cmdlets**) pass **objects** between each other, not plain text. This makes filtering and processing structured data much more reliable.

```powershell
Get-Process | Where-Object { $_.CPU -gt 100 }
```

---

## 21.2 Cmdlet Naming

Cmdlets follow a `Verb-Noun` pattern, e.g. `Get-Item`, `Set-Location`, `New-Item`. Common verbs: `Get`, `Set`, `New`, `Remove`, `Start`, `Stop`.

```powershell
Get-ChildItem                 # like ls
Set-Location C:\Projects      # like cd
New-Item -ItemType Directory -Name "backups"
Remove-Item file.txt
```

---

## 21.3 Variables and Basic Syntax

```powershell
$name = "Alice"
$count = 5
Write-Host "Hello, $name. Count: $count"

if ($count -gt 0) {
    Write-Host "Positive"
}

foreach ($i in 1..5) {
    Write-Host "Iteration $i"
}
```

Comparison operators use letters, not symbols: `-eq`, `-ne`, `-gt`, `-lt`, `-ge`, `-le`.

---

## 21.4 The Pipeline

```powershell
Get-Process | Sort-Object CPU -Descending | Select-Object -First 5
```

Because objects (not text) flow through the pipeline, `Select-Object` can pick specific properties without any text parsing.

---

[Previous](./[20]-Working-with-APIs-and-HTTP-Requests-in-Scripts.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[22]-Automating-Windows-Tasks-with-PowerShell.md)
