[Previous](./[30]-Network-Automation-Scripts.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[32]-Version-Control-for-Scripts.md)

*System Administration Scripting*

# Lesson 31 - Backup & Cleanup Scripts

## 31.1 A Basic Backup Script

```bash
#!/bin/bash
set -euo pipefail

SRC="/home/user/data"
DEST="/mnt/backups"
DATE=$(date +%Y%m%d)

tar -czf "$DEST/data_$DATE.tar.gz" "$SRC"
echo "Backup complete: $DEST/data_$DATE.tar.gz"
```

---

## 31.2 Rotating Old Backups

Keeping backups forever wastes disk space. Delete backups older than N days:

```bash
find "$DEST" -name "data_*.tar.gz" -mtime +30 -delete
```

`-mtime +30` matches files last modified more than 30 days ago.

---

## 31.3 Cleaning Temporary Files

```bash
find /tmp -type f -atime +7 -delete    # delete files not accessed in 7 days
find . -name "*.pyc" -delete            # remove compiled Python bytecode
find . -type d -name "__pycache__" -exec rm -rf {} +
```

---

## 31.4 Backups on Windows with PowerShell

```powershell
$date = Get-Date -Format "yyyyMMdd"
Compress-Archive -Path "C:\Data" -DestinationPath "D:\Backups\data_$date.zip"

# Remove backups older than 30 days
Get-ChildItem "D:\Backups" -Filter "*.zip" |
    Where-Object { $_.LastWriteTime -lt (Get-Date).AddDays(-30) } |
    Remove-Item
```

Always test that a backup can actually be **restored** — an untested backup is not a reliable one.

---

[Previous](./[30]-Network-Automation-Scripts.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[32]-Version-Control-for-Scripts.md)
