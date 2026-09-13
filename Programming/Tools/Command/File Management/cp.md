[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

# cp (copy)

`cp` copies files and directories from one location to another, leaving the original in place. It's the command-line equivalent of copy-paste.

Download: [https://man7.org/linux/man-pages/man1/cp.1.html](https://man7.org/linux/man-pages/man1/cp.1.html)

---

## What Is cp?

By default `cp` only copies individual files — copying an entire folder requires the `-r` (recursive) flag, since folders can contain their own subfolders and files that need to be copied along with them.

---

## Core Commands

```bash
cp file.txt backup.txt              # Copy a file, giving the copy a new name
cp file.txt /path/to/folder/        # Copy a file into a folder, keeping its name
cp -r folder/ backup_folder/        # Recursively copy an entire folder
cp -i file.txt existing.txt         # Prompt before overwriting an existing file
cp -v file.txt backup.txt           # Print a confirmation as the copy happens
```

---

## The -r Flag Is Not Optional for Folders

```bash
cp folder/ backup/
# cp: -r not specified; omitting directory 'folder/'

cp -r folder/ backup/
# Works: copies the folder and everything inside it
```

Forgetting `-r` when copying a directory is one of the most common `cp` mistakes — the command silently skips the folder rather than copying it.

---

## Example Walkthrough

```bash
cp -r project/ project_backup/
cp project/config.yaml project/config.yaml.bak
```

Makes a full backup copy of an entire project folder, then separately backs up just one important config file with a `.bak` suffix before making risky changes to it.

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
