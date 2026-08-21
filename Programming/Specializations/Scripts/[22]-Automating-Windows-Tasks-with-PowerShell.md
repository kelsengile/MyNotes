[Previous](./[21]-PowerShell-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[23]-PowerShell-Modules-and-Cmdlets.md)

*PowerShell Scripting*

# Lesson 22 - Automating Windows Tasks With PowerShell

## 22.1 Working with Files

```powershell
Get-ChildItem -Path C:\Logs -Filter *.log
Copy-Item file.txt backup.txt
Move-Item file.txt archive\file.txt
Remove-Item file.txt -Force
```

---

## 22.2 Managing Services

```powershell
Get-Service -Name "wuauserv"
Start-Service -Name "wuauserv"
Stop-Service -Name "wuauserv"
Restart-Service -Name "wuauserv"
```

---

## 22.3 Managing Processes

```powershell
Get-Process | Where-Object { $_.CPU -gt 200 }
Stop-Process -Name "notepad" -Force
```

---

## 22.4 A Complete Example: Cleaning Temp Files

```powershell
$tempPath = "C:\Windows\Temp"
$daysOld = 7

Get-ChildItem -Path $tempPath -File |
    Where-Object { $_.LastWriteTime -lt (Get-Date).AddDays(-$daysOld) } |
    Remove-Item -Force

Write-Host "Cleanup complete."
```

Run scripts with `powershell.exe -File script.ps1`. Note that PowerShell's default **execution policy** may block scripts from running — see Lesson 36 for the security implications of changing it.

---

[Previous](./[21]-PowerShell-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[23]-PowerShell-Modules-and-Cmdlets.md)
