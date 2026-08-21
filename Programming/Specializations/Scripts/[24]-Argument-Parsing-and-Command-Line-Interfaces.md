[Previous](./[23]-PowerShell-Modules-and-Cmdlets.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[25]-Working-with-JSON-CSV-and-XML-in-Scripts.md)

*Advanced Scripting Concepts*

# Lesson 24 - Argument Parsing & Command-Line Interfaces

## 24.1 Positional Arguments in Bash

```bash
#!/bin/bash
echo "Script name: $0"
echo "First arg: $1"
echo "All args: $@"
echo "Arg count: $#"
```

---

## 24.2 Parsing Flags in Bash with getopts

```bash
while getopts "n:v" opt; do
    case $opt in
        n) name="$OPTARG" ;;
        v) verbose=true ;;
        *) echo "Usage: $0 [-n name] [-v]"; exit 1 ;;
    esac
done

echo "Name: $name, Verbose: $verbose"
```

Run as: `./script.sh -n Alice -v`

---

## 24.3 Argument Parsing in Python with argparse

```python
import argparse

parser = argparse.ArgumentParser(description="Backup a directory")
parser.add_argument("source", help="Directory to back up")
parser.add_argument("-o", "--output", default="./backups", help="Output directory")
parser.add_argument("-v", "--verbose", action="store_true")

args = parser.parse_args()
print(args.source, args.output, args.verbose)
```

`argparse` automatically generates `--help` output and validates required arguments — far more robust than manual parsing for anything beyond a couple of flags.

---

## 24.4 Argument Parsing in PowerShell

```powershell
param(
    [Parameter(Mandatory=$true)]
    [string]$Source,

    [string]$Output = ".\backups",
    [switch]$Verbose
)

Write-Host "Source: $Source, Output: $Output, Verbose: $Verbose"
```

Run as: `.\script.ps1 -Source C:\Data -Verbose`

---

[Previous](./[23]-PowerShell-Modules-and-Cmdlets.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[25]-Working-with-JSON-CSV-and-XML-in-Scripts.md)
