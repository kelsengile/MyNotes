[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# systemctl

`systemctl` is the command-line interface for managing `systemd`, the service manager used by most modern Linux distributions to start, stop, and monitor background services.

Download: [https://www.freedesktop.org/software/systemd/man/latest/systemctl.html](https://www.freedesktop.org/software/systemd/man/latest/systemctl.html)

---

## What Is systemctl?

Nearly everything that runs in the background on a modern Linux server — web servers, databases, SSH — is managed as a `systemd` **service** (technically a "unit"). `systemctl` is how you start, stop, restart, enable, and check the status of those services.

---

## Core Commands

```bash
sudo systemctl start nginx        # Start a service
sudo systemctl stop nginx          # Stop a service
sudo systemctl restart nginx       # Restart a service
sudo systemctl status nginx        # Check whether a service is running, plus recent logs
sudo systemctl enable nginx        # Make a service start automatically on boot
sudo systemctl disable nginx       # Stop a service from starting automatically on boot
systemctl list-units --type=service   # List all currently loaded services
```

---

## Enable vs. Start — A Common Mix-Up

`systemctl start` runs a service right now, but it won't survive a reboot unless you also `enable` it. `systemctl enable` sets it to start on boot, but doesn't start it immediately unless you also `start` it (or use `--now` to do both at once).

```bash
sudo systemctl enable --now nginx
```

---

## Example Walkthrough

```bash
sudo systemctl status nginx
sudo systemctl restart nginx
sudo systemctl status nginx
```

Checks whether a web server is running, restarts it after a config change, then checks its status again to confirm it came back up cleanly.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
