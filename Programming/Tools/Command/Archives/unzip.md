[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# unzip

`unzip` extracts files from `.zip` archives on the command line, and can also inspect an archive's contents without extracting anything.

Download: [https://infozip.sourceforge.net/](https://infozip.sourceforge.net/)

---

## What Is unzip?

`unzip` is the counterpart to `zip`: it reads a `.zip` archive's central directory (an index stored at the end of the file listing every entry, its size, and where its compressed data lives) and uses that to extract, list, or test the contents without needing to scan the whole file sequentially.

---

## Core Commands

```bash
unzip archive.zip                    # Extract everything into the current directory
unzip archive.zip -d target/         # Extract into a specific target directory
unzip -l archive.zip                 # List contents without extracting
unzip -o archive.zip                 # Overwrite existing files without prompting
unzip -n archive.zip                 # Never overwrite existing files
unzip -p archive.zip file.txt        # Print a single file's contents to stdout
unzip -t archive.zip                 # Test archive integrity
unzip archive.zip "*.txt"            # Extract only files matching a pattern
unzip -x archive.zip file.txt        # Extract everything except one file
```

---

## All Major Options

| Flag | Meaning |
|---|---|
| `-l` | List contents (name, size, date) without extracting |
| `-v` | Verbose listing, including compression ratio per file |
| `-d dir` | Extract into a specific directory |
| `-o` | Overwrite files without asking |
| `-n` | Skip files that already exist |
| `-u` | Update — extract only newer files, or files not yet present |
| `-p` | Extract to stdout (pipe-friendly) |
| `-t` | Test archive integrity without extracting |
| `-q` | Quiet mode |
| `-j` | Junk paths — flatten directory structure on extraction |
| `-x file` | Exclude specific files from extraction |
| `-P pass` | Supply a password inline (insecure — visible in shell history) |

---

## Inspecting Before Extracting

A good habit before extracting an unfamiliar archive is to check what's inside first, so you don't scatter dozens of files into your current folder by accident:

```bash
unzip -l suspicious.zip
```

This prints file names, sizes, and modification dates without writing anything to disk — the safest first move with any archive from an untrusted source.

---

## Piping and Scripting Uses

```bash
unzip -p logs.zip access.log | grep "ERROR"     # Search inside a zipped file without extracting it
unzip -o build.zip -d /tmp/build && cd /tmp/build  # Extract and immediately move into it
```

`-p` is particularly useful for quickly grepping or `awk`-processing a single file buried inside an archive, since it avoids writing a temporary copy to disk.

---

## Handling Encrypted Archives

```bash
unzip archive.zip          # Prompts interactively for a password if the archive is encrypted
```

Passing the password with `-P` on the command line works but leaves the password visible in your shell history and in `ps` output while the command runs — letting `unzip` prompt interactively is the safer default.

---

## Common Gotchas

- Filename encoding: older zip archives created on Windows may show garbled filenames on Linux due to differing default character encodings; the `-O CP437` or `-O UTF-8` flags can correct this.
- Zip bombs: an archive can decompress to a size vastly larger than the archive itself. Running `unzip -l` first, or `zipinfo`, lets you sanity-check uncompressed sizes before extracting an archive from an unknown source.

---

## Example Walkthrough

```bash
unzip -l website.zip
unzip website.zip -d ./site-restored
```

Lists an archive's contents to confirm what it holds, then extracts everything into a new folder rather than dumping files into the current directory.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
EOF

cat > "/home/claude/work/Archives/tar.md" << 'EOF'
[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# tar (Tape Archive)

`tar` bundles multiple files and directories into a single archive file, preserving permissions, ownership, and directory structure. It's the standard packaging format on Unix-like systems, almost always paired with a compressor like `gzip` or `xz`.

Download: [https://www.gnu.org/software/tar/](https://www.gnu.org/software/tar/)

---

## What Is tar?

`tar` was originally built for writing sequential archives to magnetic tape — hence the name — which is why it processes files as one continuous stream rather than an indexed structure like `.zip`. It does not compress by default; it just concatenates files together with metadata headers. Compression is normally layered on afterward (`tar.gz`, `tar.bz2`, `tar.xz`), which is why you'll almost always see a compression flag alongside `tar`'s core options.

---

## Core Commands

```bash
tar -cf archive.tar file1 file2      # Create an archive (c = create, f = file)
tar -xf archive.tar                  # Extract an archive
tar -tf archive.tar                  # List contents without extracting
tar -czf archive.tar.gz folder/      # Create and gzip-compress in one step
tar -xzf archive.tar.gz              # Extract a gzip-compressed archive
tar -cjf archive.tar.bz2 folder/     # Create using bzip2 compression
tar -cJf archive.tar.xz folder/      # Create using xz compression (best ratio, slower)
tar -xvf archive.tar                 # Extract with verbose (per-file) output
```

---

## Understanding the Flag Letters

`tar`'s flags are traditionally combined without dashes in classic usage (`tar cvf`), though modern GNU tar accepts both styles:

| Letter | Meaning |
|---|---|
| `c` | Create a new archive |
| `x` | Extract an archive |
| `t` | List (table of) contents |
| `f` | Use the file named next (almost always required) |
| `v` | Verbose — print each file as it's processed |
| `z` | Compress/decompress with gzip |
| `j` | Compress/decompress with bzip2 |
| `J` | Compress/decompress with xz |
| `r` | Append files to an existing archive |
| `u` | Update — append only files newer than the archive's copy |
| `p` | Preserve file permissions on extraction |
| `C dir` | Change to a directory before extracting/creating |

---

## Extracting Into a Specific Directory

```bash
tar -xzf archive.tar.gz -C /opt/myapp
```

`-C` changes `tar`'s working directory before extraction, which avoids the common two-step of extracting into the current folder and then manually moving everything.

---

## Selective Extraction and Creation

```bash
tar -tzf archive.tar.gz | grep "config"     # Find a specific file inside a large archive
tar -xzf archive.tar.gz path/to/one/file.txt  # Extract just one file by its archived path
tar -czf backup.tar.gz --exclude="*.log" project/  # Exclude a pattern while archiving
```

---

## Working With Compression Levels

```bash
gzip -9 -c archive.tar > archive.tar.gz     # Manually control gzip's compression level
tar -c folder/ | xz -9 -T0 > archive.tar.xz # Pipe through xz with all CPU cores (-T0)
```

Piping `tar`'s output through a compressor separately (instead of using `-z`/`-J`) gives access to extra options like `xz`'s multi-threading flag (`-T0`), which `tar`'s built-in shortcuts don't expose.

---

## Preserving Permissions and Symlinks

By default GNU `tar` preserves permissions, ownership (when run as root), and symbolic links as-is rather than following them. `--dereference` (`-h`) can be used to archive the actual files a symlink points to instead of the link itself, which matters when backing up a project that includes symlinked dependencies.

---

## Common Gotchas

- Archive format detection: many modern `tar` implementations auto-detect the compression from the file extension even without `-z`/`-j`/`-J`, but relying on the explicit flag is more portable across systems.
- Absolute paths: archiving with absolute paths (`tar -cf a.tar /home/user/file`) embeds those exact paths, which can overwrite unrelated files on extraction elsewhere — GNU tar strips a leading `/` by default and warns about it.

---

## Example Walkthrough

```bash
tar -czf backup-$(date +%F).tar.gz ~/projects
tar -tzf backup-*.tar.gz | head
```

Creates a compressed, date-stamped backup of a projects folder, then peeks at the first few entries of the resulting archive to confirm it was built correctly.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
EOF

cat > "/home/claude/work/Archives/7-Zip.md" << 'EOF'
[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# 7-Zip (7z)

7-Zip is a free, open-source archiver known for its very high compression ratios, its native `.7z` format, and its `7z` command-line tool, which can read and write a wide range of archive formats.

Download: [https://www.7-zip.org/](https://www.7-zip.org/)

---

## What Is 7-Zip?

The `.7z` format uses the LZMA/LZMA2 compression algorithm, which typically compresses better than `.zip`'s DEFLATE algorithm, at the cost of being slower and using more memory during compression. The `7z` command-line utility isn't limited to its own format — it can extract from `.zip`, `.tar`, `.rar` (extraction only), `.gz`, `.iso`, and many other archive and disk-image formats through one consistent interface.

---

## Core Commands

```bash
7z a archive.7z file1 file2       # Add files to a new or existing archive
7z x archive.7z                   # Extract with full paths
7z e archive.7z                   # Extract without paths (flatten into current folder)
7z l archive.7z                   # List contents
7z t archive.7z                   # Test archive integrity
7z d archive.7z file.txt          # Delete a file from an archive
7z u archive.7z file.txt          # Update a file in the archive
```

---

## Command Letters at a Glance

| Command | Meaning |
|---|---|
| `a` | Add files to an archive |
| `x` | eXtract with full directory structure preserved |
| `e` | Extract, ignoring directory structure |
| `l` | List archive contents |
| `t` | Test archive integrity |
| `d` | Delete files from an archive |
| `u` | Update files already in an archive |
| `b` | Benchmark compression/decompression speed |

---

## Compression Levels and Formats

```bash
7z a -mx=9 archive.7z folder/         # Maximum compression (mx0=none, mx9=ultra)
7z a -t7z archive.7z folder/          # Force the .7z format
7z a -tzip archive.zip folder/        # Create a .zip instead of .7z using the same tool
7z a -mmt=4 archive.7z folder/        # Use 4 threads for compression
```

`-mx` ranges from 0 (store only) to 9 (ultra compression); higher levels trade CPU time and RAM for a smaller archive, which matters most on very large datasets.

---

## Password Protection

```bash
7z a -p -mhe=on secret.7z file.txt    # Prompt for a password and encrypt filenames too
7z a -pMyPassword secret.7z file.txt  # Supply a password inline (visible in shell history)
```

`7z`'s AES-256 encryption is genuinely strong, unlike classic `zip -e`. The `-mhe=on` flag additionally encrypts the archive's file list, so an attacker without the password can't even see what files are inside.

---

## Splitting Large Archives

```bash
7z a -v100m archive.7z bigfolder/
```

Splits the output into 100MB volumes (`archive.7z.001`, `archive.7z.002`, ...), useful for archives too large for a single upload or removable media with a size limit.

---

## Extracting Other Archive Types

```bash
7z x archive.rar        # Extract a RAR archive (7z can read, not create, RAR)
7z x disk.iso -o output/  # Extract the contents of an ISO disk image
7z x archive.tar.gz     # 7z can peel off gzip and then tar in sequence
```

Because `7z` understands so many container and compression formats, it's a common fallback tool when the "correct" native tool for a format (like `unrar`) isn't installed.

---

## Common Gotchas

- `e` vs `x`: using `e` on an archive with a nested folder structure dumps every file into one flat directory, which can silently overwrite files with the same name from different subfolders — `x` is the safer default for extracting into a preserved layout.
- GUI vs CLI: on Windows, 7-Zip's GUI installs separately from `7z.exe`/`7za.exe`; scripting requires locating or adding the command-line executable to your PATH.

---

## Example Walkthrough

```bash
7z a -mx=9 -p project-backup.7z project/
7z t project-backup.7z
```

Creates a maximally compressed, password-protected archive of a project folder, then tests its integrity to confirm nothing was corrupted during compression.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)