[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# kill

`kill` sends a signal to a process, most commonly to ask it to terminate — despite the name, `kill` is really a general-purpose "send a signal" command, and termination is just the most common signal used.

Download: [https://man7.org/linux/man-pages/man1/kill.1.html](https://man7.org/linux/man-pages/man1/kill.1.html)

---

## What Is kill?

Every process on a Unix-like system can receive **signals** — asynchronous notifications from the kernel or other processes that request some kind of action, from "please shut down" to "reload your configuration." `kill` is the tool for sending these signals manually by process ID (PID). A well-behaved program can choose how to respond to most signals (for example, saving state before exiting when it receives a termination request), except for a couple of signals the kernel doesn't allow programs to intercept or ignore.

---

## Core Commands

```bash
kill 1234                # Send the default signal (SIGTERM) to process 1234
kill -9 1234              # Send SIGKILL — force immediate termination
kill -15 1234             # Explicitly send SIGTERM (same as no flag)
kill -l                   # List all available signal names and numbers
kill -HUP 1234            # Send SIGHUP, often used to make a daemon reload its config
killall firefox            # Kill all processes matching a name
pkill -f "python script.py" # Kill processes matching a pattern in their full command line
```

---

## Common Signals

| Signal | Number | Meaning |
|---|---|---|
| `SIGTERM` | 15 | Default — politely ask a process to terminate |
| `SIGKILL` | 9 | Force-kill immediately, cannot be caught or ignored |
| `SIGHUP` | 1 | Hangup — often repurposed by daemons to mean "reload config" |
| `SIGINT` | 2 | Interrupt — same signal Ctrl+C sends |
| `SIGSTOP` | 19 | Pause a process (cannot be caught or ignored) |
| `SIGCONT` | 18 | Resume a previously stopped process |

---

## SIGTERM vs SIGKILL

```bash
kill 1234       # SIGTERM: "please shut down" — the process can catch this and clean up
kill -9 1234    # SIGKILL: "die now" — the kernel terminates it immediately, no cleanup
```

`SIGTERM` is the polite, default request, giving a well-written program the chance to close files, save state, or finish an in-progress operation before exiting. `SIGKILL` bypasses the process entirely and is handled by the kernel — it's a last resort for processes that are unresponsive or ignoring `SIGTERM`, since it doesn't allow any graceful cleanup and can, in rare cases, leave things in an inconsistent state (like a partially written file).

---

## Finding the Right Process to Signal

```bash
pgrep -f "node server.js"
ps aux | grep node
```

`kill` requires a PID, so it's almost always paired with `ps`/`pgrep` to first find the process ID of whatever you want to signal — `pgrep -f` matches against the full command line, which is more precise than a plain `grep` on `ps` output when several similarly-named processes are running.

---

## killall and pkill: Killing by Name

```bash
killall chrome              # Kill every process literally named "chrome"
pkill -f "backup.sh"         # Kill any process whose full command line matches a pattern
pkill -u alice                # Kill all processes owned by a specific user
```

`killall` matches by exact process name, while `pkill` supports more flexible pattern matching (including against the full command line with `-f`) — useful when a process was launched with arguments that make its name alone ambiguous.

---

## Common Gotchas

- Reaching for SIGKILL too early: `SIGKILL` should generally be a last resort after `SIGTERM` has been given a moment to work, since it prevents any graceful shutdown logic from running.
- Killing the wrong process: `killall`/`pkill` matching more broadly than intended (especially with `-f` and a loose pattern) can terminate unrelated processes that happen to match — always double-check the match with `pgrep`/`ps` first before actually killing.

---

## Example Walkthrough

```bash
pgrep -f "node server.js"
kill 4821
sleep 2
kill -9 4821 2>/dev/null
```

Finds a specific Node.js server process, asks it to shut down gracefully, waits briefly, then force-kills it if it's still running — a common pattern in deploy scripts for restarting a service cleanly but reliably.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
EOF

cat > "/home/claude/work/System Administration/systemctl.md" << 'EOF'
[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# systemctl

`systemctl` controls `systemd`, the init system and service manager used by most modern Linux distributions — starting, stopping, enabling, and inspecting background services.

Download: [https://www.freedesktop.org/wiki/Software/systemd/](https://www.freedesktop.org/wiki/Software/systemd/)

---

## What Is systemctl?

`systemd` is the first process started when a modern Linux system boots (PID 1), responsible for bringing up all other services in the right order, tracking their state, restarting them if they crash, and managing dependencies between them. `systemctl` is the primary command-line interface for interacting with it — checking a service's status, starting/stopping it, or configuring whether it should launch automatically at boot.

---

## Core Commands

```bash
systemctl status nginx           # Show detailed status of a service
systemctl start nginx             # Start a service now
systemctl stop nginx              # Stop a service now
systemctl restart nginx           # Stop and start a service
systemctl reload nginx            # Reload config without a full restart (if supported)
systemctl enable nginx            # Make a service start automatically at boot
systemctl disable nginx           # Remove a service from automatic startup
systemctl is-active nginx         # Quick check: is it currently running?
systemctl is-enabled nginx        # Quick check: will it start at boot?
```

---

## Understanding start/stop vs enable/disable

This is the single most important distinction to internalize: `start`/`stop` affect whether a service is running **right now**, while `enable`/`disable` affect whether it will be started automatically **at the next boot**. A service can be started but not enabled (running now, won't survive a reboot), or enabled but not started (will launch on next boot, not running currently) — the two states are independent.

```bash
systemctl enable --now nginx     # Enable AND start in one command
systemctl disable --now nginx    # Disable AND stop in one command
```

---

## Reading systemctl status

```
● nginx.service - A high performance web server
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled)
     Active: active (running) since Mon 2026-09-01 08:00:00 UTC; 2 weeks ago
   Main PID: 1234 (nginx)
      Tasks: 3
     Memory: 4.2M
```

- **Loaded** shows where the unit file lives and whether it's enabled.
- **Active** shows the current run state (`active (running)`, `inactive (dead)`, `failed`, etc.) and how long it's been in that state.
- **Main PID** is the primary process ID systemd is tracking for this service.

---

## Viewing Service Logs

```bash
journalctl -u nginx              # Show all logs for a specific unit
journalctl -u nginx -f            # Follow logs live (like tail -f)
journalctl -u nginx --since today # Filter logs by time
```

Because systemd captures the stdout/stderr of every service it manages, `journalctl -u <service>` is the standard way to see a service's logs — it's covered in more detail on the `journalctl` page.

---

## Listing Services

```bash
systemctl list-units --type=service           # All currently loaded services
systemctl list-units --type=service --state=running  # Only running services
systemctl list-unit-files --type=service      # All installed service definitions, enabled or not
```

---

## Editing a Service (Unit File)

```bash
sudo systemctl edit nginx           # Create an override file for a specific unit
sudo systemctl edit --full nginx    # Edit the entire unit file directly
sudo systemctl daemon-reload        # Reload unit files after any manual edit
```

`daemon-reload` is required any time a unit file is manually edited or created — systemd caches unit definitions, and without this step, changes won't take effect even if the underlying file has been saved.

---

## System-Wide Power Commands

```bash
systemctl reboot                  # Reboot the system
systemctl poweroff                 # Shut down the system
systemctl suspend                  # Suspend to RAM
```

---

## Common Gotchas

- Forgetting `daemon-reload`: a manually edited unit file has no effect until systemd is told to reload its configuration.
- Confusing `restart` with `reload`: `restart` fully stops and starts a service (briefly dropping connections for something like a web server), while `reload` (only supported by services designed for it) applies configuration changes without interrupting the running process — using `reload` where possible avoids unnecessary downtime.

---

## Example Walkthrough

```bash
sudo systemctl enable --now nginx
systemctl status nginx
journalctl -u nginx -f
```

Enables and starts a web server so it survives reboots, checks its current status, then follows its logs live — a typical sequence when standing up a new service on a Linux server.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)