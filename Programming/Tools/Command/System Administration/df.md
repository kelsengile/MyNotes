[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# df (disk free)

`df` reports how much disk space is used and available on each mounted filesystem. It's usually the first command run when a machine complains about running out of space.

Download: [https://man7.org/linux/man-pages/man1/df.1.html](https://man7.org/linux/man-pages/man1/df.1.html)

---

## What Is df?

A single machine can have several filesystems mounted at once — the main drive, a separate data partition, a mounted network share — and `df` shows the space usage of each one individually, rather than just a single overall number.

---

## Core Commands

```bash
df                  # Show disk usage in 1K blocks (hard to read)
df -h                 # Show usage in human-readable form (GB, MB)
df -h /home           # Show usage for the filesystem containing a specific path
df -i                  # Show inode usage instead of space (relevant for "disk full" with space left)
```

`-h` is almost always worth adding — without it, output is in raw block counts that are difficult to interpret at a glance.

---

## Reading the Output

```
Filesystem      Size  Used  Avail  Use%  Mounted on
/dev/sda1        50G   32G    16G   67%  /
/dev/sdb1       200G  180G    10G   95%  /data
```

`Use%` above roughly 90% is generally worth investigating before it becomes a real problem, since many services fail unpredictably once a disk fills completely.

---

## Example Walkthrough

```bash
df -h
```

A single glance at every mounted filesystem's usage percentage — often enough on its own to identify which drive is close to full before diving deeper with `du` to find exactly what's taking up the space.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
