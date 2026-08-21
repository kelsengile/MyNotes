[Previous](./[13]-Writing-Your-First-Automation-Script.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[15]-Environment-Variables-and-Configuration-Files.md)

*Automation Basics*

# Lesson 14 - Scheduling Scripts (cron, Task Scheduler)

## 14.1 cron (Linux/macOS)

`cron` runs scripts on a schedule defined in a **crontab**. Edit your crontab with:

```bash
crontab -e
```

Each line has five time fields followed by the command:

```
# minute hour day month weekday   command
0 2 * * *  /home/user/backup.sh          # every day at 2:00 AM
*/15 * * * * /home/user/check.sh          # every 15 minutes
0 9 * * 1   /home/user/weekly_report.sh   # every Monday at 9:00 AM
```

Always use absolute paths in cron jobs — cron runs with a minimal environment, so relative paths and unset `PATH` variables are a common source of failures.

---

## 14.2 systemd Timers (Linux Alternative)

Modern Linux systems often use `systemd` timers instead of cron, which offer logging via `journalctl` and more flexible scheduling. This is set up with a pair of unit files (`.service` and `.timer`) rather than a single crontab line.

---

## 14.3 Task Scheduler (Windows)

On Windows, **Task Scheduler** (GUI) or the `schtasks` command schedules scripts, including PowerShell scripts:

```powershell
schtasks /create /tn "DailyBackup" /tr "powershell.exe -File C:\scripts\backup.ps1" /sc daily /st 02:00
```

---

## 14.4 Best Practices for Scheduled Scripts

- Log output to a file so you can debug failures after the fact.
- Make scripts idempotent (safe to run more than once).
- Send a notification or write to a monitored log on failure.

---

[Previous](./[13]-Writing-Your-First-Automation-Script.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[15]-Environment-Variables-and-Configuration-Files.md)
