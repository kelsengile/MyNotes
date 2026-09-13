[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

# journalctl

`journalctl` queries and displays logs collected by `systemd`'s logging system, the journal — including logs from services managed by `systemctl`, kernel messages, and boot logs.

Download: [https://www.freedesktop.org/software/systemd/man/latest/journalctl.html](https://www.freedesktop.org/software/systemd/man/latest/journalctl.html)

---

## What Is journalctl?

Rather than each service writing its own plain-text log file in its own location, `systemd`-based systems centralize logging into a single structured journal. `journalctl` is the tool for searching, filtering, and following that journal.

---

## Core Commands

```bash
journalctl                        # Show the entire journal (oldest first)
journalctl -u nginx                 # Show logs for a specific service only
journalctl -f                        # Follow the journal live, like tail -f
journalctl -u nginx -f               # Follow a specific service's logs live
journalctl --since "1 hour ago"      # Show logs from a relative time range
journalctl -p err                    # Show only error-level (or worse) messages
journalctl -b                         # Show logs from the current boot only
```

---

## Filtering by Time

```bash
journalctl -u myapp --since "2026-09-13 08:00" --until "2026-09-13 09:00"
```

Narrows results to a specific time window — invaluable when tracking down what happened around a known incident time, instead of scrolling through an entire service's history.

---

## Example Walkthrough

```bash
journalctl -u myapp -p err --since "1 hour ago"
```

Shows only error-level log entries from a specific service over the last hour — a fast way to check whether anything's gone wrong recently without wading through routine informational log lines.

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
