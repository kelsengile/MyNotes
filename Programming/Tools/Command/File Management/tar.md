[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

# tar (Tape Archive)

`tar` bundles multiple files and folders into a single archive file. It shows up here in File Management because bundling and unbundling files is a routine part of everyday file organization — for the full breakdown of compression flags and formats, see its entry under [Archives](../Archives/tar.md).

Download: [https://www.gnu.org/software/tar/](https://www.gnu.org/software/tar/)

---

## Quick Reference

```bash
tar -cvf archive.tar folder/     # Bundle a folder into an archive
tar -xvf archive.tar             # Extract an archive
tar -tvf archive.tar             # List contents without extracting
tar -czvf archive.tar.gz folder/ # Bundle and compress with gzip
```

---

## Why It's Listed Here Too

Everyday file management often means packaging a project folder to send to someone, or unpacking something you downloaded — both jobs `tar` handles constantly, alongside more dedicated archive-management tasks like the ones covered in the [Archives](../Archives/tar.md) section.

---

## Example Walkthrough

```bash
tar -czvf project_snapshot.tar.gz project/
```

Creates a single compressed file capturing the current state of a project folder — a quick, no-fuss way to back up or hand off a folder's contents.

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
