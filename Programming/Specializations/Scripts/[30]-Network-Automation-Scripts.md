[Previous](./[29]-Process-and-Service-Management-Scripts.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[31]-Backup-and-Cleanup-Scripts.md)

*System Administration Scripting*

# Lesson 30 - Network Automation Scripts

## 30.1 Basic Network Diagnostics

```bash
ping -c 4 example.com
curl -I https://example.com          # fetch headers only
traceroute example.com
netstat -tulpn                       # listening ports (or `ss -tulpn` on modern systems)
```

---

## 30.2 Checking If a Host or Port Is Reachable

```bash
if nc -z -w3 example.com 443; then
    echo "Port 443 is open"
else
    echo "Port 443 is closed or host unreachable"
fi
```

---

## 30.3 A Simple Uptime Monitor in Python

```python
import requests
import time

URL = "https://example.com"

while True:
    try:
        r = requests.get(URL, timeout=5)
        status = "UP" if r.status_code == 200 else f"DEGRADED ({r.status_code})"
    except requests.RequestException:
        status = "DOWN"

    print(f"{time.ctime()}: {URL} is {status}")
    time.sleep(60)
```

---

## 30.4 Network Automation in PowerShell

```powershell
Test-Connection -ComputerName example.com -Count 4
Test-NetConnection -ComputerName example.com -Port 443
Get-NetIPConfiguration
```

Network automation scripts are commonly used for monitoring uptime, validating firewall rules, and auditing open ports across many machines.

---

[Previous](./[29]-Process-and-Service-Management-Scripts.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[31]-Backup-and-Cleanup-Scripts.md)
