[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# kill

`kill` sends a signal to a running process, most often to ask it to stop. Despite the name, `kill` doesn't only terminate processes — it can send any signal, including ones that just ask a process to reload its configuration.

Download: [https://man7.org/linux/man-pages/man1/kill.1.html](https://man7.org/linux/man-pages/man1/kill.1.html)

---

## What Is kill?

Every process can be sent a "signal" — a small message telling it to do something, like shut down gracefully or stop for a moment. `kill` is simply the tool for sending these signals by process ID (PID), which you typically find first using `ps` or `top`.

---

## Core Commands

```bash
kill 1234                  # Send the default signal (SIGTERM) — ask the process to stop gracefully
kill -9 1234                # Send SIGKILL — force-stop immediately, no cleanup
kill -l                      # List all available signal names
killall firefox               # Kill every process matching a name, instead of a specific PID
pkill -f "python app.py"      # Kill processes whose full command line matches a pattern
```

---

## SIGTERM vs. SIGKILL

`kill 1234` (SIGTERM) politely asks a process to shut down, giving it a chance to save data and clean up. `kill -9 1234` (SIGKILL) terminates it immediately with no chance to clean up — a last resort for a process that's ignoring SIGTERM or completely frozen.

---

## Example Walkthrough

```bash
ps aux | grep myapp
kill 4821
sleep 5
kill -9 4821
```

Finds a stuck application's PID, asks it to shut down gracefully, waits a few seconds, then force-kills it only if it's still running — the standard escalation pattern for stopping an unresponsive process.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
