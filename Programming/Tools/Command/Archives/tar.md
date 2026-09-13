[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

# tar (Tape Archive)

`tar` is the standard Unix/Linux utility for bundling multiple files and folders into a single archive file, optionally compressing it along the way. It's the backbone of almost every `.tar`, `.tar.gz`, and `.tar.bz2` file you'll come across on Linux and macOS.

Download: [https://www.gnu.org/software/tar/](https://www.gnu.org/software/tar/)

---

## What Is tar?

The name comes from "Tape ARchive" — it was originally built to write data to tape drives. Today it's used to package a directory tree into one portable file, which can then be compressed with a separate tool like `gzip` or `bzip2`. `tar` itself doesn't compress; it just bundles. Compression is bolted on through flags.

---

## Core Commands

```bash
tar -cvf archive.tar folder/        # Create an archive from a folder
tar -xvf archive.tar                # Extract an archive into the current folder
tar -tvf archive.tar                # List the contents without extracting
tar -czvf archive.tar.gz folder/    # Create a gzip-compressed archive
tar -xzvf archive.tar.gz            # Extract a gzip-compressed archive
tar -cjvf archive.tar.bz2 folder/   # Create a bzip2-compressed archive
tar -xjvf archive.tar.bz2           # Extract a bzip2-compressed archive
tar -xvf archive.tar -C /path/      # Extract into a specific directory
```

The flags read like a sentence: `c` create, `x` extract, `t` list, `v` verbose, `f` "the next argument is the filename", `z` gzip, `j` bzip2.

---

## Reading the Flags

- **`f`** must always be immediately followed by the archive filename — it's easy to forget and end up with `tar` trying to write to your terminal instead of a file.
- **`v`** is optional but recommended while learning; it prints every file as it's processed so you can see what's happening.
- Combining single-letter flags (`-xzvf`) is just shorthand for `-x -z -v -f`.

---

## Example Walkthrough

```bash
tar -czvf backup.tar.gz Documents/
tar -tvf backup.tar.gz
tar -xzvf backup.tar.gz -C /tmp/restore/
```

Compresses the `Documents` folder into `backup.tar.gz`, lists its contents to confirm everything's there, then extracts it into a separate restore folder.

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
