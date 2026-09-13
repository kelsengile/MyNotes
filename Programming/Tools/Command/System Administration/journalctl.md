[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# journalctl

`journalctl` queries and displays logs collected by `systemd-journald`, the logging component of systemd — the standard way to view system and service logs on most modern Linux distributions.

Download: [https://www.freedesktop.org/software/systemd/man/journalctl.html](https://www.freedesktop.org/software/systemd/man/journalctl.html)

---

## What Is journalctl?

Traditionally, Linux logs were plain text files under `/var/log`, managed separately by whatever tool wrote them. `systemd-journald` instead collects logs from the kernel, systemd services, and other sources into a structured, indexed binary format (the "journal"), and `journalctl` is the query tool for reading it — supporting filtering by time, service, priority, and boot session in ways that grepping plain text files can't do as cleanly.

---

## Core Commands

```bash
journalctl                        # Show all logs, oldest first
journalctl -f                      # Follow logs live (like tail -f)
journalctl -u nginx                 # Show logs for a specific systemd unit
journalctl -b                       # Show logs only from the current boot
journalctl -b -1                     # Show logs from the previous boot
journalctl --since "1 hour ago"      # Filter by relative time
journalctl --since "2026-09-01" --until "2026-09-02"  # Filter by a date range
```

---

## Filtering by Service

```bash
journalctl -u nginx
journalctl -u nginx -u postgresql     # Multiple units at once
journalctl -u nginx -f                 # Follow a specific service's logs live
```

`-u` is one of the most-used flags, since it narrows the enormous system-wide journal down to just the service you're actually debugging.

---

## Filtering by Priority

```bash
journalctl -p err                  # Show only error-level messages and above
journalctl -p warning -b            # Warnings and above, from the current boot
```

Priority levels follow the standard syslog scale: `emerg`, `alert`, `crit`, `err`, `warning`, `notice`, `info`, `debug` — filtering by `-p` cuts through noisy informational logs to surface only genuinely concerning messages.

---

## Time-Based Filtering

```bash
journalctl --since today
journalctl --since "10 minutes ago"
journalctl --since "2026-09-13 08:00:00" --until "2026-09-13 09:00:00"
```

`--since`/`--until` accept both relative expressions ("yesterday", "1 hour ago") and absolute timestamps, making it easy to zoom into exactly the window around when a problem occurred.

---

## Boot-Specific Logs

```bash
journalctl --list-boots         # List all recorded boot sessions
journalctl -b                    # Current boot
journalctl -b -1                  # Previous boot
```

This is especially useful for diagnosing a crash or unexpected reboot — `journalctl -b -1` shows exactly what was happening right up until the last shutdown or crash of the previous session.

---

## Output Formats

```bash
journalctl -o json                # Structured JSON output, one entry per line
journalctl -o json-pretty          # Same, formatted for readability
journalctl -o cat                  # Just the message text, no metadata
```

JSON output is particularly useful when feeding journal entries into another tool (like a log aggregator or a script parsing specific fields) rather than reading them directly.

---

## Managing Journal Disk Usage

```bash
journalctl --disk-usage             # Show how much disk space the journal is using
sudo journalctl --vacuum-time=2weeks  # Delete entries older than 2 weeks
sudo journalctl --vacuum-size=500M    # Shrink the journal to a maximum size
```

The journal can grow quite large on a busy, long-running system — `--vacuum-time`/`--vacuum-size` are the standard ways to reclaim disk space without disabling logging entirely.

---

## Common Gotchas

- Persistent vs volatile storage: on some systems the journal is stored only in memory/tmpfs and cleared on reboot unless persistent storage is explicitly configured (`/var/log/journal` must exist with the right permissions) — worth checking if `journalctl -b -1` unexpectedly returns nothing.
- Needing sudo: reading the full system journal (rather than just your own user's logs) typically requires elevated privileges or membership in the `systemd-journal` group.

---

## Example Walkthrough

```bash
journalctl -u nginx -p err --since "1 hour ago"
```

Shows only error-level log entries for the Nginx service from the past hour — a fast, targeted way to investigate a service that just started misbehaving.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)