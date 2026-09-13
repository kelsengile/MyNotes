[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# zip

`zip` is a command-line utility for creating `.zip` archives — one of the most widely recognized archive formats, readable natively by Windows, macOS, and Linux without any extra software.

Download: [https://infozip.sourceforge.net/](https://infozip.sourceforge.net/)

---

## What Is zip?

Unlike `tar`, which bundles first and compresses separately, `zip` compresses each file individually as it adds them to the archive. This makes it easy to extract or even peek at a single file inside a large `.zip` without processing the whole thing.

---

## Core Commands

```bash
zip archive.zip file1 file2         # Add specific files to a new archive
zip -r archive.zip folder/          # Recursively zip an entire folder
zip -e archive.zip file.txt         # Create a password-protected archive
zip -u archive.zip file.txt         # Update a file already in the archive
zip -d archive.zip file.txt         # Delete a file from an archive
zip -9 archive.zip file.txt         # Maximum compression (slower)
```

---

## Recursion Matters

Forgetting `-r` is the most common `zip` mistake — without it, `zip` only adds files directly named on the command line, silently skipping the contents of any folder you point it at.

```bash
zip project.zip project/        # WRONG — only adds an empty folder entry
zip -r project.zip project/     # RIGHT — includes everything inside
```

---

## Example Walkthrough

```bash
zip -r website.zip site/ -x "site/node_modules/*"
unzip -l website.zip
```

Zips the `site` folder recursively while excluding `node_modules`, then lists the archive's contents to confirm the exclusion worked.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
