[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# tar (tape archive)

`tar` bundles multiple files and directories into a single archive file, preserving their structure, permissions, and metadata. Despite the name — a holdover from its 1979 origins writing archives to magnetic tape drives — it remains the standard packaging format across nearly all Unix-like systems today.

Reference: [https://www.gnu.org/software/tar/manual/tar.html](https://www.gnu.org/software/tar/manual/tar.html)

---

## What Is tar?

`tar` itself only concatenates files together with metadata headers into one stream — it doesn't compress anything on its own. Compression is layered on separately (traditionally via a pipe to `gzip`, `bzip2`, or `xz`), which is why you'll see file extensions like `.tar.gz` ("tarball," then gzipped) or `.tar.xz`. Modern GNU `tar` bundles this into single flags (`-z`, `-j`, `-J`) so you don't have to pipe manually, but conceptually the two steps — archiving and compressing — remain distinct, which is different from formats like `.zip` where archiving and compression are the same step.

---

## Core Commands

```bash
tar -cvf archive.tar folder/            # Create an archive (no compression)
tar -xvf archive.tar                    # Extract an archive
tar -tvf archive.tar                    # List contents without extracting
tar -czvf archive.tar.gz folder/        # Create, compressed with gzip
tar -xzvf archive.tar.gz                # Extract a gzip-compressed archive
tar -cjvf archive.tar.bz2 folder/       # Create, compressed with bzip2
tar -cJvf archive.tar.xz folder/        # Create, compressed with xz (best ratio, slower)
tar -xzvf archive.tar.gz -C /target/dir # Extract into a specific directory
tar -czvf archive.tar.gz --exclude="*.log" folder/  # Exclude matching files while archiving
tar -rvf archive.tar newfile.txt        # Append a file to an existing (uncompressed) archive
tar -xzvf archive.tar.gz file/inside/archive.txt  # Extract just one file from the archive
```

## Core Flags

| Flag | Meaning |
|---|---|
| `-c` | Create a new archive |
| `-x` | Extract from an archive |
| `-t` | List contents (table of contents) |
| `-v` | Verbose — print each file as it's processed |
| `-f FILE` | The archive filename (almost always required, must usually come last among short flags) |
| `-z` | Filter through gzip (`.tar.gz` / `.tgz`) |
| `-j` | Filter through bzip2 (`.tar.bz2`) |
| `-J` | Filter through xz (`.tar.xz`) |
| `-C DIR` | Change to DIR before extracting (controls destination) |
| `-r` | Append files to an existing archive |
| `--exclude=PATTERN` | Skip matching files |
| `-p` | Preserve permissions exactly (important when restoring as root) |

---

## Compression Format Trade-offs

| Extension | Algorithm | Speed | Ratio |
|---|---|---|---|
| `.tar.gz` / `.tgz` | gzip | Fast | Moderate |
| `.tar.bz2` | bzip2 | Slower | Better than gzip |
| `.tar.xz` | xz (LZMA2) | Slowest to compress | Best ratio |
| `.tar.zst` | zstd | Very fast, both directions | Excellent ratio for the speed |

`gzip` remains the most universally compatible default; `xz` is favored for distributing software releases where a smaller download matters more than compression time; `zstd` (via `tar --zstd`, on newer `tar` versions) has become popular for its speed without giving up much ratio, especially for frequent backups.

---

## Beyond Basic Archiving

- **Preserving full metadata for backups**: `tar` can preserve permissions, ownership, timestamps, extended attributes, ACLs, and even sparse-file structure (`-S`) — making it a genuine backup tool, not just a packaging format. Full-system backups piped through `tar` (sometimes over the network via `ssh`) remain common.
- **Incremental archives** (`--listed-incremental=snapshot-file`): `tar` can create archives containing only files changed since a previous snapshot, the basis for many custom incremental backup scripts.
- **Streaming without a temp file**: `tar` can read from and write to stdin/stdout, enabling direct pipelines like `tar czf - folder/ | ssh remote 'cat > backup.tar.gz'` to archive, compress, and transfer to a remote machine in a single pass with no intermediate file.
- **Multi-volume archives** (`-M`) for splitting an archive across multiple physical media, a direct descendant of `tar`'s original tape-based purpose.
- **Selective extraction and listing**: extracting or listing just a subset of paths from a large archive without unpacking the whole thing.

---

## Common Pitfalls

- **Flag order with `-f`**: in the traditional single-dash cluster style (`tar -xzvf file.tar.gz`), `f` must be the last letter since the archive filename immediately follows it — `tar -fxzv file.tar.gz` would incorrectly treat `xzv` as the filename.
- **Absolute paths in archives**: by default GNU `tar` strips leading slashes and warns about it, to avoid an extraction accidentally overwriting arbitrary absolute system paths.
- **Extracting untrusted archives**: as with any archive format, extracting a tarball from an untrusted source can overwrite files via crafted relative paths (`../../etc/passwd`); modern `tar` refuses path traversal outside the destination by default, but it's still worth being cautious.
- **Compression choice matters for huge datasets**: choosing `xz` for a backup you need to create quickly and often can be a mistake — the compression ratio gain isn't always worth the CPU time compared to `gzip` or `zstd`.

---

## Related Tools

- `zip` / `unzip` — a self-contained archive+compression format, more common on Windows, with per-file compression (allows random access to individual files without decompressing the whole archive, unlike `tar.gz`).
- `gzip` / `bzip2` / `xz` / `zstd` — the standalone compression tools `tar` delegates to.
- `7z` (7-Zip) — a modern, high-ratio archiver popular especially on Windows, supporting its own `.7z` format as well as reading/writing `tar`.
- `rsync` — for ongoing synchronization rather than one-shot archiving; often used alongside `tar` for backups (`rsync` for incremental transfer, `tar` for a final consolidated snapshot).

---

## Example Walkthrough

```bash
tar -czvf website_backup_2026-09-13.tar.gz --exclude="*.log" /var/www/html
tar -tzvf website_backup_2026-09-13.tar.gz | head
tar -xzvf website_backup_2026-09-13.tar.gz -C /restore/location
```

Creates a compressed, dated backup of a website directory while skipping log files, previews the archive's contents without extracting anything, then later restores it into a specific target directory — the typical create → verify → restore cycle for a `tar`-based backup.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)