[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# mkdir (make directory)

`mkdir` creates new directories (folders). It's one of the most frequently used file management commands, since almost every task starts with organizing files into folders.

Download: [https://man7.org/linux/man-pages/man1/mkdir.1.html](https://man7.org/linux/man-pages/man1/mkdir.1.html)

---

## What Is mkdir?

By default, `mkdir` creates exactly one folder, and it will fail if the parent folder doesn't already exist. Its most useful flag, `-p`, changes both behaviors — creating an entire chain of nested folders in one go.

---

## Core Commands

```bash
mkdir project                     # Create a single folder
mkdir folder1 folder2 folder3     # Create several folders at once
mkdir -p src/components/ui        # Create nested folders, creating parents as needed
mkdir -v new_folder                # Print a confirmation message for each folder created
```

---

## Why -p Matters

```bash
mkdir project/src/utils
# mkdir: cannot create directory 'project/src/utils': No such file or directory

mkdir -p project/src/utils
# Works: creates project/, project/src/, and project/src/utils/ all at once
```

Without `-p`, `mkdir` refuses to create a folder if its parent doesn't exist yet. With `-p`, it silently creates every missing folder along the path.

---

## Example Walkthrough

```bash
mkdir -p my_app/{src,tests,docs}
ls my_app
```

Creates a project skeleton with `src`, `tests`, and `docs` subfolders in a single command using brace expansion, then lists the result to confirm all three were created.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
