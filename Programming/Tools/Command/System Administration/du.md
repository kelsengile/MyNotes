[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

# du (disk usage)

`du` estimates how much disk space specific files and folders are using. Where `df` reports space for an entire filesystem, `du` drills down into exactly which folders are responsible for that usage.

Download: [https://man7.org/linux/man-pages/man1/du.1.html](https://man7.org/linux/man-pages/man1/du.1.html)

---

## What Is du?

Once `df` tells you a drive is nearly full, `du` is how you figure out *what's* filling it — by recursively totaling the size of every file inside a folder, and optionally its subfolders individually.

---

## Core Commands

```bash
du -h file.txt                 # Human-readable size of a single file
du -sh folder/                  # Total size of a folder, summarized (not per-subfolder)
du -h folder/                   # Size of every subfolder individually, recursively
du -sh */                        # Total size of every folder in the current directory
du -sh * | sort -rh | head -10   # Find the 10 largest items in the current folder
```

`-s` summarizes into one total instead of listing every nested folder; `-h` makes sizes human-readable (KB/MB/GB).

---

## Finding What's Eating Your Disk

```bash
du -sh /var/log/* | sort -rh | head -10
```

Lists the size of everything inside `/var/log`, sorts by size (largest first), and shows the top 10 — a classic way to hunt down a runaway log file that filled up a disk.

---

## Example Walkthrough

```bash
du -sh ~/Downloads/*  | sort -rh | head -5
```

Shows the 5 largest items inside a Downloads folder, sorted biggest first — a quick way to reclaim space without manually clicking through folders in a file browser.

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
