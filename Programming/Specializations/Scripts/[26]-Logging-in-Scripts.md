[Previous](./[25]-Working-with-JSON-CSV-and-XML-in-Scripts.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[27]-Debugging-Scripts.md)

*Advanced Scripting Concepts*

# Lesson 26 - Logging In Scripts

## 26.1 Why Log Instead of Just Printing

Unattended scripts (cron jobs, scheduled tasks) have no one watching the terminal. Logging to a file with timestamps and severity levels is essential for debugging failures after the fact.

---

## 26.2 Simple Logging in Bash

```bash
log() {
    echo "$(date '+%Y-%m-%d %H:%M:%S') [$1] $2" >> script.log
}

log "INFO" "Starting backup"
log "ERROR" "Backup failed: disk full"
```

---

## 26.3 Logging in Python with the logging Module

```python
import logging

logging.basicConfig(
    filename="script.log",
    level=logging.INFO,
    format="%(asctime)s [%(levelname)s] %(message)s"
)

logging.info("Starting backup")
logging.warning("Low disk space")
logging.error("Backup failed")
```

The `logging` module supports levels (`DEBUG`, `INFO`, `WARNING`, `ERROR`, `CRITICAL`), so verbosity can be adjusted without changing the code, only the configured level.

---

## 26.4 Logging in PowerShell

```powershell
"$(Get-Date -Format 'yyyy-MM-dd HH:mm:ss') [INFO] Starting backup" | Out-File -Append script.log
Start-Transcript -Path "script.log" -Append   # captures all console output automatically
# ... script logic ...
Stop-Transcript
```

---

## 26.5 Log Rotation

Logs that grow forever will eventually fill a disk. Use tools like `logrotate` (Linux) to automatically archive and truncate log files on a schedule.

---

[Previous](./[25]-Working-with-JSON-CSV-and-XML-in-Scripts.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[27]-Debugging-Scripts.md)
