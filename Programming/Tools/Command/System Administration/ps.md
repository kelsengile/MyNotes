[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# ps (process status)

`ps` shows a snapshot of currently running processes — their IDs, resource usage, and status — at the moment the command runs.

Download: [https://man7.org/linux/man-pages/man1/ps.1.html](https://man7.org/linux/man-pages/man1/ps.1.html)

---

## What Is ps?

Unlike `top`, which continuously refreshes a live view, `ps` prints a single point-in-time snapshot and exits, which makes it well suited for scripting: its output can be piped into `grep`, `awk`, or `sort` to extract exactly the process information a script needs, without the interactive noise `top` produces.

---

## Core Commands

```bash
ps                    # Show processes for the current terminal session
ps aux                 # Show all processes from every user, in a detailed BSD-style format
ps -ef                 # Show all processes in a detailed System V-style format
ps aux | grep firefox   # Find a specific process by name
ps -p 1234              # Show info for a specific process ID
```

---

## Understanding aux vs -ef

Both `aux` and `-ef` show all processes system-wide, but come from two historically different `ps` styles (BSD vs System V) with slightly different column sets and formatting conventions. Modern `ps` implementations (like Linux's `procps`) support both styles for compatibility — `aux` is more common in casual use, `-ef` is common in scripts because its column layout is slightly more consistent for parsing (particularly the parent process ID column, `PPID`).

---

## Reading ps aux Output

```
USER   PID  %CPU %MEM    VSZ   RSS TTY   STAT START   TIME COMMAND
alice  1234  2.3  1.5  204832 51200 pts/0 S    09:00   0:15 firefox
```

| Column | Meaning |
|---|---|
| `PID` | Process ID |
| `%CPU` | Percentage of CPU currently used |
| `%MEM` | Percentage of physical memory used |
| `VSZ` | Virtual memory size |
| `RSS` | Resident Set Size — actual physical RAM in use |
| `STAT` | Process state (see below) |
| `TIME` | Total CPU time consumed since the process started |

---

## Process States

| Code | Meaning |
|---|---|
| `R` | Running or runnable |
| `S` | Sleeping (waiting for an event) |
| `D` | Uninterruptible sleep (usually waiting on I/O) |
| `Z` | Zombie — finished but not yet reaped by its parent |
| `T` | Stopped (e.g. suspended with Ctrl+Z) |
| `<` | High priority process |
| `N` | Low priority (niced) process |

A large number of processes stuck in `D` state often points to disk or network I/O problems, and lingering `Z` (zombie) processes usually indicate a parent process that isn't properly waiting for its children to finish.

---

## Sorting and Filtering

```bash
ps aux --sort=-%cpu | head -10     # Top 10 CPU-consuming processes
ps aux --sort=-%mem | head -10     # Top 10 memory-consuming processes
ps -eo pid,ppid,cmd --forest       # Show a process tree with parent/child relationships
```

`--forest` is especially useful for understanding which processes spawned which others — critical when trying to figure out exactly what to kill to stop a whole family of related processes cleanly.

---

## Custom Output Columns

```bash
ps -eo pid,user,%cpu,%mem,cmd
```

`-o` (or `-eo` for all processes) lets you specify exactly which columns to show, in whatever order — useful in scripts that need to extract, say, just the PID and command name without parsing through the full default column set.

---

## Common Gotchas

- Snapshot, not live: values like `%CPU` reflect an instantaneous or short-window measurement at the moment `ps` ran, not a continuous average — a process can look idle in one `ps` call and busy in the next even a second apart.
- Finding the right process to kill: `ps aux | grep processname` also matches the `grep` command itself in the results unless filtered out (`grep -v grep` or using `pgrep` instead), a very common minor gotcha.

---

## Example Walkthrough

```bash
ps aux --sort=-%mem | head -5
```

Lists the five processes currently consuming the most memory — a common first diagnostic step when a machine feels sluggish or is running low on RAM.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
EOF

cat > "/home/claude/work/System Administration/top.md" << 'EOF'
[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# top

`top` displays a continuously updating, live view of running processes and overall system resource usage (CPU, memory) directly in the terminal.

Download: [https://man7.org/linux/man-pages/man1/top.1.html](https://man7.org/linux/man-pages/man1/top.1.html)

---

## What Is top?

Where `ps` gives a single snapshot, `top` refreshes its display at a regular interval (by default every few seconds), letting you watch resource usage change in real time and interactively sort, filter, or act on processes without leaving the screen. It's usually the very first tool reached for when a machine "feels slow" and you need to see immediately what's consuming resources right now.

---

## Launching and Basic Usage

```bash
top                # Launch the live process viewer
top -u alice        # Show only processes owned by a specific user
top -p 1234          # Monitor a specific process ID
top -d 5             # Set the refresh interval to 5 seconds
```

---

## Reading the Header

```
top - 14:32:01 up 3 days,  4:12,  2 users,  load average: 0.52, 0.58, 0.61
Tasks: 210 total,   1 running, 209 sleeping,   0 stopped,   0 zombie
%Cpu(s):  8.3 us,  2.1 sy,  0.0 ni, 89.1 id,  0.4 wa,  0.0 hi,  0.1 si
MiB Mem :  16000.0 total,   4200.0 free,   8800.0 used,   3000.0 buff/cache
```

- **load average** — three numbers for the 1, 5, and 15-minute average system load; a load average near or above the number of CPU cores suggests the system is saturated.
- **%Cpu(s)** breakdown — `us` (user processes), `sy` (kernel/system), `id` (idle), `wa` (waiting on I/O); high `wa` often points to a disk bottleneck rather than a CPU one.
- **Mem** — total, free, used, and cache/buffer memory; Linux aggressively uses free RAM for disk caching, so "used" memory looking high isn't necessarily a problem if most of it is reclaimable cache.

---

## Interactive Keys

| Key | Action |
|---|---|
| `q` | Quit |
| `k` | Kill a process (prompts for PID and signal) |
| `M` | Sort by memory usage |
| `P` | Sort by CPU usage (often the default) |
| `1` | Toggle showing per-core CPU breakdown instead of an aggregate |
| `f` | Choose which columns are displayed |
| `u` | Filter to a specific user's processes |
| `r` | Renice (change priority of) a running process |
| `space` | Force an immediate refresh |

---

## Killing a Process From Within top

```
k
1234
15
```

Pressing `k` inside `top` prompts for a PID and then a signal number to send — `15` (SIGTERM, the default) asks the process to shut down gracefully, while `9` (SIGKILL) forces immediate termination, the same distinction that applies when using the standalone `kill` command.

---

## htop as a Modern Alternative

Many systems also have `htop` available (a separate, often-installed-separately tool), which offers a friendlier color interface, mouse support, and easier process searching and killing than classic `top` — worth knowing about as the more approachable option when it's installed.

---

## Common Gotchas

- Misreading load average without knowing core count: a load average of 4 is fine on a 8-core machine but means serious saturation on a 2-core one — always interpret load average relative to the number of CPU cores.
- Confusing used memory with a problem: high "used" memory that's mostly disk cache (visible in the `buff/cache` figure) is normal and healthy, not a sign of a memory leak.

---

## Example Walkthrough

```bash
top -o %MEM
```

Launches `top` sorted by memory usage from the start, immediately surfacing the biggest memory consumers on the system — useful when investigating a machine that's running low on RAM.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)