[Previous](./[22]-Automating-Windows-Tasks-with-PowerShell.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[24]-Argument-Parsing-and-Command-Line-Interfaces.md)

*PowerShell Scripting*

# Lesson 23 - PowerShell Modules & Cmdlets

## 23.1 What Is a Module?

A **module** is a package of related cmdlets, functions, and variables — similar to a library in Python. PowerShell ships with many built-in modules (`Microsoft.PowerShell.Management`, `ActiveDirectory`, etc.).

```powershell
Get-Module -ListAvailable       # list installed modules
Import-Module ActiveDirectory   # load a module
```

---

## 23.2 Discovering Cmdlets

```powershell
Get-Command -Module Microsoft.PowerShell.Management
Get-Help Get-ChildItem -Full
Get-Help Get-ChildItem -Examples
```

`Get-Help` is the PowerShell equivalent of `man` — always check it before guessing at parameters.

---

## 23.3 Installing Modules from the Gallery

```powershell
Install-Module -Name Az -Scope CurrentUser
Import-Module Az
```

The **PowerShell Gallery** is the standard repository for community and vendor modules (similar to PyPI for Python or npm for JavaScript).

---

## 23.4 Writing Your Own Functions

```powershell
function Get-DiskSpaceReport {
    param(
        [string]$Drive = "C:"
    )
    Get-PSDrive -Name $Drive.TrimEnd(':') |
        Select-Object Name, @{Name="FreeGB";Expression={[math]::Round($_.Free/1GB,2)}}
}

Get-DiskSpaceReport -Drive "C:"
```

Functions with `param()` blocks behave like cmdlets, including support for named parameters.

---

[Previous](./[22]-Automating-Windows-Tasks-with-PowerShell.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[24]-Argument-Parsing-and-Command-Line-Interfaces.md)
