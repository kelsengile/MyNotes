[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# tail

`tail` prints the last lines (by default, the last 10) of one or more files or of standard input. Its `-f` ("follow") mode, which streams new lines as they're written, makes it the single most common tool for watching live log files.

Reference: [https://man7.org/linux/man-pages/man1/tail.1.html](https://man7.org/linux/man-pages/man1/tail.1.html)

---

## What Is tail?

Reading the *end* of a file is fundamentally harder than reading the beginning — you generally need to know how long the file is first. For regular files `tail` handles this efficiently by seeking near the end (using the file's size) rather than reading the whole thing from the start; for pipes or streams where seeking isn't possible, it has to keep a rolling buffer of the last N lines while reading forward.

`tail -f` goes further: after printing the last lines, it keeps the file open and polls for new data being appended, printing each new line as it arrives — turning a static file view into a live feed. This is how developers and sysadmins watch logs in real time.

---

## Core Commands

```bash
tail file.txt                  # Print the last 10 lines
tail -n 20 file.txt             # Print the last 20 lines
tail -n +5 file.txt             # Print starting from line 5 to the end
tail -c 200 file.txt            # Print the last 200 bytes
tail -f app.log                 # Follow a file, printing new lines as they're written
tail -F app.log                 # Follow, and re-attach if the file is rotated/recreated
tail -f -n 0 app.log             # Follow only new lines, don't print existing history first
```

## Full Option Reference

| Flag | Long form | Meaning |
|---|---|---|
| `-n NUM` | `--lines=NUM` | Print the last NUM lines (`+NUM` means "start at line NUM") |
| `-c NUM` | `--bytes=NUM` | Print the last NUM bytes |
| `-f` | `--follow` | Keep the file open and print appended data as it's written |
| `-F` | | Like `-f`, but also tracks the file by name, reopening it if it's rotated, truncated, or replaced |
| `-q` | `--quiet` | Suppress filename headers with multiple files |
| `-s SEC` | `--sleep-interval=SEC` | How often to poll for new data in follow mode |
| `--pid=PID` | | In follow mode, stop once the given process ID exits |

---

## Watching Logs Live

```bash
tail -f /var/log/syslog
```

This is arguably `tail`'s most famous use case — leaving a terminal open showing new log entries as an application runs, essential for debugging servers, watching deployments, or monitoring background jobs. `-F` is generally preferred over `-f` for genuine log-watching, because log rotation tools (like `logrotate`) frequently rename or truncate the file `tail` is watching, and only `-F` follows the *name*, transparently reopening the new file.

Combine with `grep` for a filtered live view:

```bash
tail -f /var/log/nginx/access.log | grep --line-buffered "500"
```

(`--line-buffered` matters here — without it, `grep`'s output buffering can delay matches from appearing until a large chunk has accumulated.)

---

## Multiple Files at Once

```bash
tail -f app.log error.log
```

`tail` interleaves output from both files as new lines appear in either, prefixing each block with a `==> filename <==` header — a lightweight way to watch two related logs side by side in one terminal.

---

## Related Tools

- `head` — the mirror-image tool for the beginning of a file.
- `less +F file.txt` — `less`'s built-in follow mode, offering the ability to scroll back and forth as well as follow.
- `journalctl -f` — the systemd-journal equivalent of `tail -f` for services that log via `systemd-journald` rather than a plain text file.
- `watch` — re-runs an entire command periodically, useful when there's no append-only file to follow.

---

## Example Walkthrough

```bash
tail -n 50 deploy.log
tail -F deploy.log | grep -i error
```

Reviews the last 50 lines of a deployment log to get context, then leaves a live filtered view running that only prints new lines containing "error" — surviving even if the log file gets rotated mid-deployment.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)