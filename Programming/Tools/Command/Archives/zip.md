[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# zip

`zip` is a command-line utility for creating `.zip` archives — one of the most widely recognized archive formats, readable natively by Windows, macOS, and Linux without any extra software.

Download: [https://infozip.sourceforge.net/](https://infozip.sourceforge.net/)

---

## What Is zip?

Unlike `tar`, which bundles first and compresses separately, `zip` compresses each file individually as it adds them to the archive. This per-file compression is what makes `.zip` archives good for random access: a program can jump straight to one file's compressed data and decompress just that file, instead of having to read through the whole archive as `tar.gz` requires. The trade-off is a slightly worse overall compression ratio, since compressing files individually gives the compressor less shared context than compressing the whole bundle as one stream.

---

## Core Commands

```bash
zip archive.zip file1 file2         # Add specific files to a new archive
zip -r archive.zip folder/          # Recursively zip an entire folder
zip -e archive.zip file.txt         # Create a password-protected archive
zip -u archive.zip file.txt         # Update a file already in the archive (only if changed)
zip -d archive.zip file.txt         # Delete a file from an archive
zip -9 archive.zip file.txt         # Maximum compression (slower)
zip -0 archive.zip file.txt         # Store only, no compression (fastest)
zip -m archive.zip file.txt         # Move files into the archive, deleting the originals
zip -x "*.log" -r archive.zip dir/  # Exclude a pattern while zipping
zip -j archive.zip path/to/file.txt # Junk paths — store the file without its folder structure
zip -T archive.zip                  # Test the archive's integrity after creation
```

---

## All Major Options

| Flag | Meaning |
|---|---|
| `-r` | Recurse into directories |
| `-e` / `-P pass` | Encrypt with a prompted or inline password |
| `-u` | Update — replace only files that changed |
| `-f` | Freshen — like `-u` but only updates files already in the archive |
| `-d` | Delete entries matching a pattern |
| `-x pattern` | Exclude files matching a pattern |
| `-i pattern` | Include only files matching a pattern |
| `-0` to `-9` | Compression level, none to maximum |
| `-j` | Junk (discard) directory paths, storing files flat |
| `-m` | Move — delete originals after adding to the archive |
| `-q` | Quiet mode, suppress normal output |
| `-v` | Verbose mode, or show version info with no other args |
| `-sf` | Show the list of files that would be included, without archiving |
| `-z` | Add a comment to the archive |
| `-A` | Adjust a self-extracting archive after modification |

---

## Recursion Matters

Forgetting `-r` is the most common `zip` mistake — without it, `zip` only adds files directly named on the command line, silently skipping the contents of any folder you point it at.

```bash
zip project.zip project/        # WRONG — only adds an empty folder entry
zip -r project.zip project/     # RIGHT — includes everything inside
```

---

## Splitting, Comments, and Self-Extracting Archives

```bash
zip -s 100m -r bigarchive.zip folder/   # Split output into 100MB volumes
zip -z archive.zip                       # Prompt to add/edit an archive comment
zip -A selfextract.exe                   # Repair offsets after editing a self-extracting zip
```

Splitting is useful for archives too large to email or upload in one piece — Windows and most modern unzip tools can reassemble split volumes automatically as long as all the pieces are present.

---

## Password Protection Caveats

`zip -e` uses the format's original, weak ZipCrypto encryption, which is trivially breakable with modern tools if an attacker has the file. For anything genuinely sensitive, encrypt with a stronger tool (like GPG) before zipping, or use `7z`'s AES-256 option instead of relying on `zip -e` for real security.

---

## Combining zip With Other Tools

```bash
find . -name "*.log" | zip logs.zip -@      # Zip a list of files piped in from find
zip -r - folder/ | ssh user@host "cat > backup.zip"   # Stream a zip archive over SSH without a local temp file
```

The `-@` flag tells `zip` to read the list of filenames from standard input instead of the command line, which is handy when the file list comes from `find`, `grep -l`, or another command.

---

## Example Walkthrough

```bash
zip -r website.zip site/ -x "site/node_modules/*"
unzip -l website.zip
```

Zips the `site` folder recursively while excluding `node_modules`, then lists the archive's contents to confirm the exclusion worked.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)