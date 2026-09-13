[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# systemctl

`systemctl` is the primary command for controlling `systemd`, the init system and service manager used by most modern Linux distributions (Ubuntu, Debian, Fedora, RHEL/CentOS, Arch, and more). It starts, stops, enables, and inspects services, and manages much of the boot process itself.

Reference: [https://www.freedesktop.org/software/systemd/man/systemctl.html](https://www.freedesktop.org/software/systemd/man/systemctl.html)

---

## What Is systemctl?

`systemd` replaced the older SysV-init and Upstart systems on most distributions over the 2010s, becoming the process with PID 1 — the very first process the kernel starts, responsible for bringing up every other service and managing the system's state throughout its life, not just at boot. Rather than running numbered shell scripts in `/etc/init.d/`, `systemd` describes services declaratively in **unit files** (plain text configuration files, typically ending in `.service`, `.socket`, `.timer`, `.mount`, and others) and manages dependencies, parallel startup, automatic restarts, and resource limits based on them. `systemctl` is the command-line interface to all of that.

---

## Core Commands

```bash
systemctl status nginx           # Show a service's current state and recent log lines
systemctl start nginx            # Start a service now
systemctl stop nginx             # Stop a service now
systemctl restart nginx          # Stop then start
systemctl reload nginx           # Ask the service to reload its config without a full restart
systemctl enable nginx           # Make the service start automatically at boot
systemctl disable nginx          # Remove it from starting at boot
systemctl enable --now nginx     # Enable AND start it immediately, in one command
systemctl list-units --type=service   # List all currently loaded service units
systemctl list-unit-files --state=enabled  # List all units enabled to start at boot
systemctl is-active nginx        # Just print "active" or "inactive"
systemctl is-enabled nginx       # Just print "enabled" or "disabled"
systemctl daemon-reload          # Reload unit files after editing them, before systemd notices
```

## Command Reference

| Command | Purpose |
|---|---|
| `start` / `stop` / `restart` | Control a service's running state right now |
| `reload` | Signal a service to re-read its configuration without restarting the process |
| `enable` / `disable` | Control whether a service starts automatically at boot |
| `mask` / `unmask` | Completely prevent a service from being started, even manually (stronger than disable) |
| `status` | Show current state, PID, memory use, and recent journal entries |
| `list-units` | List loaded units of a given type |
| `list-unit-files` | List installed unit files and their enabled/disabled state |
| `daemon-reload` | Re-read unit files after they've changed on disk |
| `edit` | Open a unit file (or an override snippet) in your editor |
| `cat` | Print a unit file's full effective content |
| `show` | Dump all low-level properties of a unit |

---

## Enable vs. Start: A Frequent Point of Confusion

- **`start`** affects the service *right now*, for the current boot session only.
- **`enable`** affects whether the service will be started automatically the *next time the system boots* — it does not start it immediately.

Running only `enable` on a freshly installed service leaves it not actually running until the next reboot; running only `start` means it won't survive a reboot. `enable --now` does both at once, which is why it's the most commonly reached-for combination when setting up a new service.

---

## Reading systemctl status

```
● nginx.service - A high performance web server
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
     Active: active (running) since Thu 2026-09-11 09:12:03 UTC; 2 days ago
   Main PID: 812 (nginx)
      Tasks: 3 (limit: 4915)
     Memory: 5.2M
        CPU: 1.203s
     CGroup: /system.slice/nginx.service
             ├─812 nginx: master process /usr/sbin/nginx
             └─813 nginx: worker process
```

The colored dot (`●`) at the top summarizes health at a glance (green = active/running, red = failed). `Loaded` shows whether the unit file was found and whether it's enabled at boot. `Active` shows the actual runtime state and how long it's held that state. Below that, `systemctl status` also tails the most recent related log entries from the journal, often the fastest way to see why a service just failed.

---

## Beyond Services: What Else systemctl Manages

- **Timers (`.timer` units)** — `systemd`'s modern replacement for `cron`, offering more expressive scheduling and, crucially, integration with the journal for logging and dependency ordering with other units.
- **Sockets (`.socket` units)** — socket-activated services that only start on first connection, saving resources for infrequently used services.
- **Mounts and automounts (`.mount`/`.automount`)** — filesystem mounts can be managed and ordered as systemd units alongside services that depend on them.
- **Targets (`.target` units)** — group multiple units together, roughly analogous to old SysV-init "runlevels" (e.g., `multi-user.target`, `graphical.target`).
- **System-wide power actions** — `systemctl reboot`, `systemctl poweroff`, `systemctl suspend`, `systemctl hibernate` are all handled through the same command.
- **Resource control** — unit files can set CPU/memory/IO limits per service via cgroups, and `systemctl set-property` can adjust these live without editing files.

---

## Related Tools

- `journalctl` — views the structured logs `systemd` collects from every unit (`journalctl -u nginx -f` to follow a specific service's logs live).
- `systemd-analyze` — measures and visualizes boot time, showing which units are slowing startup.
- `loginctl` — manages user sessions and seats under `systemd-logind`.
- `crontab` — the older, still-common scheduling tool that `systemd` timers can replace but haven't fully displaced.

---

## Example Walkthrough

```bash
systemctl enable --now postgresql
systemctl status postgresql
journalctl -u postgresql -n 50
```

Enables and immediately starts a database service, checks its current health summary, then pulls the last 50 journal lines specific to that service — a typical sequence when standing up a new service and confirming it came up cleanly.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)