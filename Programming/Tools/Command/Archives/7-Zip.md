[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# 7-Zip

7-Zip is a free, open-source file archiver best known for its own `.7z` format, which typically compresses smaller than `.zip`. Alongside its Windows GUI, it ships a command-line tool, `7z`, that works the same way on Windows, Linux, and macOS.

Download: [https://www.7-zip.org/](https://www.7-zip.org/)

---

## What Is 7-Zip?

7-Zip's `.7z` format uses the LZMA compression algorithm, which generally achieves higher compression ratios than the DEFLATE algorithm used by classic `.zip` files — at the cost of being slower and less universally supported out of the box. The `7z` command-line tool also reads and writes many other formats, including `.zip`, `.tar`, `.gzip`, and `.rar` (extract-only).

---

## Core Commands

```bash
7z a archive.7z folder/             # Add files/folders to a new .7z archive
7z x archive.7z                     # Extract with full paths preserved
7z e archive.7z                     # Extract, flattening everything into one folder
7z l archive.7z                     # List the contents of an archive
7z a -tzip archive.zip folder/      # Create a .zip instead of .7z
7z a -p archive.7z file.txt         # Create a password-protected archive
7z t archive.7z                     # Test an archive's integrity
```

---

## x vs. e — A Common Trap

`7z x` extracts an archive while keeping its internal folder structure. `7z e` extracts everything into a single flat folder, discarding that structure — which can cause files with the same name in different subfolders to overwrite each other. For anything but a single-folder archive, `x` is almost always what you want.

---

## Example Walkthrough

```bash
7z a -tzip release.zip build/
7z l release.zip
7z x release.zip -o./extracted
```

Packages the `build` folder into a `.zip`, lists its contents, then extracts it into a new `extracted` folder while preserving the original structure.

[⬅ Back to Command Fundamentals(../[0]-Introduction-to-Command.md)
