[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# top

`top` is an interactive, continuously updating view of running processes and overall system resource usage — CPU, memory, and more — refreshed several times a second.

Download: [https://man7.org/linux/man-pages/man1/top.1.html](https://man7.org/linux/man-pages/man1/top.1.html)

---

## What Is top?

Where `ps` gives you a single snapshot, `top` gives you a live dashboard: which processes are consuming the most CPU or memory right now, updated in real time, right in the terminal — often the very first thing an admin runs when a server "feels slow."

---

## Launching and Navigating

```bash
top                # Launch the live process viewer
```

**Keys inside top:**
```
q         Quit
k         Kill a process (you'll be prompted for its PID)
M         Sort by memory usage
P         Sort by CPU usage
r         Renice (change the priority) of a process
```

---

## Reading the Header

```
%Cpu(s):  12.3 us,  2.1 sy,  0.0 ni, 85.0 id
MiB Mem :   7938.4 total,   1204.2 free,   3891.0 used
```

`us` is CPU time spent on user processes, `sy` on the kernel itself, `id` is idle time. High `id` means the CPU has spare capacity; consistently low `id` means the machine is genuinely under load.

---

## Example Walkthrough

Running `top`, pressing `P` sorts the process list by CPU usage, instantly surfacing whichever process is responsible for a machine running hot — the fastest way to answer "what's eating my CPU?" without writing a single command-line flag.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
