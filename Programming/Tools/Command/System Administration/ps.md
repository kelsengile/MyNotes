[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

# ps (process status)

`ps` displays a snapshot of currently running processes — their process IDs, resource usage, and status — at the moment you run the command.

Download: [https://man7.org/linux/man-pages/man1/ps.1.html](https://man7.org/linux/man-pages/man1/ps.1.html)

---

## What Is ps?

Unlike `top` (covered separately), which updates continuously, `ps` prints one static snapshot and exits — useful for scripting, or when you just need a quick list without a live-updating display taking over your terminal.

---

## Core Commands

```bash
ps                     # Show processes running in the current terminal session
ps aux                  # Show every process on the system, in a detailed format
ps -ef                  # Same idea, in a different (POSIX-standard) format
ps aux | grep nginx     # Find a specific process by name
ps -p 1234               # Show details for a specific process ID (PID)
```

---

## Reading ps aux Output

```
USER   PID  %CPU  %MEM   VSZ   RSS TTY  STAT START   TIME COMMAND
root     1   0.0   0.1  1234   456 ?    Ss   08:00   0:01 /sbin/init
alice  982   2.3   1.4 45678  6789 pts/0 R+  09:15   0:12 python app.py
```

`PID` is the process ID you'd use with `kill`, `%CPU`/`%MEM` show resource usage, and `STAT` shows the process state (`R` running, `S` sleeping, `Z` zombie, and so on).

---

## Example Walkthrough

```bash
ps aux | grep python | grep -v grep
```

Lists every running Python process while excluding the `grep` command itself from the results (which would otherwise show up as a false match, since it also contains the word "python" in its own command line).

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
