[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# top

`top` is a real-time, interactive process viewer — it shows currently running processes sorted by resource usage, refreshing continuously, and lets you sort, filter, and even kill processes without leaving it. It's usually the very first thing a Linux admin runs when a system feels slow.

Reference: [https://man7.org/linux/man-pages/man1/top.1.html](https://man7.org/linux/man-pages/man1/top.1.html)

---

## What Is top?

`top` reads process and system information from `/proc` (on Linux) every few seconds and redraws the entire screen with an updated summary: system-wide CPU and memory usage at the top, followed by a live, sortable table of individual processes. Unlike `ps`, which takes a single snapshot and exits, `top` is a persistent, full-screen program you interact with — closer to a dashboard than a one-shot command.

---

## Launching and Reading the Display

```bash
top                     # Launch the interactive process viewer
top -u username          # Show only processes owned by a specific user
top -p 1234,5678         # Monitor only specific PIDs
top -d 5                 # Set the refresh interval to 5 seconds
top -n 1                 # Take a single snapshot and exit (useful in scripts)
top -b                   # Batch mode: plain, non-interactive output, good for logging/piping
```

The header block shows:
- **Uptime & load average** — three numbers representing average system load over the last 1, 5, and 15 minutes (roughly, how many processes were runnable/waiting on average).
- **Tasks** — total processes and how many are running, sleeping, stopped, or zombied.
- **%Cpu(s)** — CPU time breakdown: `us` (user processes), `sy` (kernel/system), `id` (idle), `wa` (waiting on I/O), and others.
- **Mem / Swap** — total, used, free, and buffered/cached memory and swap space.

The process table below shows, per process: PID, user, priority (`PR`), nice value (`NI`), virtual/resident memory (`VIRT`/`RES`), CPU%, MEM%, runtime, and command name.

---

## Interactive Keys

| Key | Effect |
|---|---|
| `P` | Sort by CPU usage (default) |
| `M` | Sort by memory usage |
| `T` | Sort by running time |
| `k` | Kill a process (prompts for PID and signal) |
| `r` | Renice a process (change its priority) |
| `u` | Filter to a specific user |
| `f` | Choose which fields/columns are displayed |
| `1` | Toggle showing per-core CPU breakdown instead of an aggregate |
| `c` | Toggle showing the full command line vs. just the process name |
| `q` | Quit |

---

## Understanding Load Average and Memory Numbers

A load average of `2.0` on a 4-core machine means the system was, on average, using half its available processing capacity — load average is relative to core count, so the same number means something very different on a 2-core versus a 32-core machine. `VIRT` (virtual memory) includes all memory a process has *mapped*, including shared libraries and unused allocations, and is usually much larger and less meaningful than `RES` (resident memory) — the actual physical RAM currently in use, which is what most people actually mean by "how much memory is this process using."

---

## Beyond Basic Monitoring

- **Sending signals interactively** — pressing `k` inside `top` and choosing a signal (like `SIGTERM` or `SIGKILL`) is a faster way to kill a misbehaving process than switching to another terminal for `kill`.
- **Renicing live** — pressing `r` lets you lower or raise a process's scheduling priority on the fly, without restarting it.
- **Batch mode for logging** — `top -b -n 1` produces a single, script-friendly snapshot, commonly cron'd to a log file for historical resource tracking.
- **Per-user monitoring** on shared/multi-user systems, to identify who's consuming the most resources.
- **Threads view** — pressing `H` toggles between per-process and per-thread display, useful for diagnosing multi-threaded applications.

---

## top vs. Its Successors

`htop` is a widely used, more polished reimplementation: color output, mouse support, a visual per-core CPU meter, easier scrolling, and tree/hierarchy views of process parent-child relationships — everything `top` can do, presented more legibly. `top` remains valuable because it's essentially guaranteed to be preinstalled on every Linux system, including minimal containers and servers where `htop` may not be available.

`glances` and `btop` go further still, adding network, disk I/O, and GPU monitoring in one unified dashboard.

---

## Related Tools

- `htop` — friendlier, more visual reimplementation of the same idea.
- `ps aux` — a one-shot, scriptable snapshot of processes, good for piping into `grep`/`awk`.
- `vmstat` — periodic system-wide virtual memory, CPU, and I/O statistics in compact rows.
- `iotop` — the `top`-style tool specifically for disk I/O per process.
- `nice` / `renice` — adjust a process's scheduling priority from the command line without an interactive tool.

---

## Example Walkthrough

```bash
top -o %MEM
```

Launches `top` sorted immediately by memory usage — a fast way to answer "what's using all my RAM?" without needing to press `M` after the fact, useful when diagnosing an out-of-memory situation under time pressure.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)