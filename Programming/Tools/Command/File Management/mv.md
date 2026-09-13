[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# mv (move)

`mv` moves files or directories to a new location, and is also how you rename them on the command line — there's no separate "rename" command on Unix-like systems.

Download: [https://man7.org/linux/man-pages/man1/mv.1.html](https://man7.org/linux/man-pages/man1/mv.1.html)

---

## What Is mv?

Unlike `cp`, `mv` doesn't leave a copy behind — the file exists only at the new location afterward. Moving a file within the same filesystem is effectively instant, since it just updates where the filesystem says the file lives, rather than physically copying its data.

---

## Core Commands

```bash
mv file.txt newname.txt            # Rename a file
mv file.txt /path/to/folder/       # Move a file into a folder, keeping its name
mv folder/ /path/to/destination/   # Move an entire folder (no -r needed, unlike cp)
mv -i file.txt existing.txt        # Prompt before overwriting an existing file
mv *.txt archive/                  # Move every .txt file into a folder
```

---

## Renaming Is Just Moving

```bash
mv draft.md final.md
```

There's no dedicated `rename` command in POSIX systems — renaming is simply moving a file to a new name in the same folder, which is exactly what `mv` does.

---

## Example Walkthrough

```bash
mv report_draft.md report_v1.md
mkdir archive
mv *.log archive/
```

Renames a draft file to reflect it's the first finished version, then sweeps every `.log` file in the current folder into a new `archive` subfolder.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
