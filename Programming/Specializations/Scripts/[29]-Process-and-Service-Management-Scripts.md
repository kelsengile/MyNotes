[Previous](./[28]-User-and-Permission-Management-Scripts.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[30]-Network-Automation-Scripts.md)

*System Administration Scripting*

# Lesson 29 - Process & Service Management Scripts

## 29.1 Inspecting Processes on Linux

```bash
ps aux                    # list all running processes
ps aux | grep nginx       # find a specific process
top                        # live view of resource usage
kill -9 1234                # forcefully stop process with PID 1234
pkill -f "python script.py" # kill by matching command name
```

---

## 29.2 Managing services with systemd

```bash
sudo systemctl status nginx
sudo systemctl start nginx
sudo systemctl stop nginx
sudo systemctl restart nginx
sudo systemctl enable nginx    # start automatically on boot
```

---

## 29.3 A Health-Check-and-Restart Script

```bash
#!/bin/bash
SERVICE="nginx"

if ! systemctl is-active --quiet "$SERVICE"; then
    echo "$(date): $SERVICE is down, restarting..." >> /var/log/watchdog.log
    sudo systemctl start "$SERVICE"
fi
```

Schedule this with cron every few minutes (Lesson 14) to build a simple watchdog.

---

## 29.4 Process and Service Management on Windows

```powershell
Get-Process | Where-Object { $_.CPU -gt 100 }
Stop-Process -Name "app" -Force

Get-Service -Name "Spooler"
Restart-Service -Name "Spooler"
```

---

[Previous](./[28]-User-and-Permission-Management-Scripts.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[30]-Network-Automation-Scripts.md)
