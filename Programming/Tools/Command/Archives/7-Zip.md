[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# 7-Zip

7-Zip is a free, open-source file archiver best known for its own `.7z` format, which typically compresses smaller than `.zip`. Alongside its Windows GUI, it ships a command-line tool, `7z`, that works the same way on Windows, Linux, and macOS.

Download: [https://www.7-zip.org/](https://www.7-zip.org/)

---

## What Is 7-Zip?

7-Zip was created by Igor Pavlov and first released in 1999. Its native `.7z` format was designed from the ground up around **LZMA**, a compression algorithm developed alongside 7-Zip itself that generally achieves higher compression ratios than the DEFLATE algorithm used by classic `.zip` files — at the cost of being slower and, historically, less universally supported out of the box (most operating systems can open `.zip` natively; `.7z` usually needs 7-Zip or a compatible tool installed).

Beyond `.7z`, the `7z` command-line tool reads and writes many other formats, making it a genuinely general-purpose archiving utility rather than a single-format tool.

**Formats it can create:** `.7z`, `.zip`, `.gzip`, `.bzip2`, `.tar`, `.wim`
**Formats it can extract (read-only):** all of the above, plus `.rar`, `.arj`, `.cab`, `.chm`, `.cpio`, `.deb`, `.dmg`, `.iso`, `.lzh`, `.lzma`, `.msi`, `.nsis`, `.rpm`, `.udf`, `.vhd`, `.xar`, and more

That broad extraction support is why 7-Zip is often kept installed even on systems that never create `.7z` archives — it's a reliable fallback for opening almost any archive format encountered in the wild.

---

## Installation

```bash
# Debian/Ubuntu — the CLI package is named p7zip
sudo apt install p7zip-full

# Fedora
sudo dnf install p7zip p7zip-plugins

# macOS (Homebrew)
brew install p7zip

# Windows
# Download the installer directly from 7-zip.org, or: choco install 7zip
```

On Linux/macOS the command is often `7z` or `7za` (a version bundled without some proprietary codec support) depending on the packaging.

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
7z d archive.7z file.txt            # Delete a file from an existing archive
7z u archive.7z newfile.txt         # Update an archive — add new/changed files only
7z rn archive.7z old.txt new.txt    # Rename a file inside an archive
```

---

## x vs. e — A Common Trap

`7z x` extracts an archive while keeping its internal folder structure. `7z e` extracts everything into a single flat folder, discarding that structure — which can cause files with the same name in different subfolders to overwrite each other. For anything but a single-folder archive, `x` is almost always what you want.

---

## Compression Levels and Methods

```bash
7z a -mx0 archive.7z folder/   # Store only, no compression (fastest)
7z a -mx1 archive.7z folder/   # Fastest compression
7z a -mx5 archive.7z folder/   # Normal (default)
7z a -mx9 archive.7z folder/   # Ultra — smallest output, slowest, most memory
```

7-Zip supports several compression **methods**, selectable independently of the level:

```bash
7z a -m0=LZMA2 archive.7z folder/    # Default and generally the best all-rounder for .7z
7z a -m0=PPMd archive.7z folder/     # Often better for text/document-heavy content
7z a -m0=BZip2 archive.7z folder/    # Good for already partially-compressed or specific data types
7z a -m0=Copy archive.7z folder/     # No compression at all, just container packaging
```

`LZMA2` (an improved, multi-threading-friendly version of the original LZMA) is the default for `.7z` and is what gives 7-Zip its reputation for high compression ratios.

---

## Multi-threading

```bash
7z a -mmt=4 archive.7z folder/   # Use 4 threads for compression
7z a -mmt=on archive.7z folder/  # Use all available cores
7z a -mmt=off archive.7z folder/ # Single-threaded (slightly better ratio, much slower)
```

Multi-threaded compression trades a small amount of compression ratio for a large speed improvement on multi-core machines, and is enabled by default on most builds.

---

## Encryption and Password Protection

```bash
7z a -p archive.7z file.txt              # Prompt for a password, encrypt file contents (AES-256)
7z a -pMyPassword archive.7z file.txt    # Supply the password inline (visible in shell history — use with caution)
7z a -p -mhe=on archive.7z file.txt      # Also encrypt filenames/headers, not just file contents
```

By default, encrypting a `.7z` archive hides file *contents* but leaves filenames and folder structure visible in the archive listing unless `-mhe=on` ("header encryption") is also set — a detail that trips people up when they assume `-p` alone hides everything. Note that `.zip` encryption (`ZipCrypto`) is much weaker than `.7z`'s AES-256 and shouldn't be relied on for sensitive data.

---

## Splitting Archives Into Volumes

```bash
7z a -v100m archive.7z folder/    # Split into 100MB volumes: archive.7z.001, .002, ...
7z a -v1440k archive.7z folder/   # Split into old-school floppy-disk-sized volumes
7z x archive.7z.001               # Extracting the first volume automatically pulls in the rest
```

Useful for archives that need to be transferred over media or channels with a file size limit (older email systems, some USB drives, certain upload limits).

---

## Self-Extracting Archives (SFX)

```bash
7z a -sfx archive.exe folder/
```

Produces a `.exe` that, when run on Windows, extracts its contents without the recipient needing 7-Zip installed — commonly used for distributing bundles to users who may not have an archiver available.

---

## Excluding Files

```bash
7z a archive.7z folder/ -xr!*.log        # Exclude all .log files, recursively
7z a archive.7z folder/ -x!node_modules  # Exclude a specific folder by name
7z a archive.7z folder/ -xr!.git         # Common pattern: exclude version-control metadata
```

---

## Benchmarking

```bash
7z b            # Run 7-Zip's built-in CPU/RAM benchmark using its own compression algorithms
```

This is sometimes used informally as a quick, repeatable CPU stress test/benchmark independent of actual archiving needs, since compression is a genuinely CPU- and memory-intensive workload.

---

## GUI-Only Features Worth Knowing About

The Windows GUI adds some conveniences not obviously exposed by the CLI:

- **Explorer integration** — right-click "7-Zip → Add to archive…" / "Extract Here" context menu entries.
- **Archive browsing** — opening a `.7z`/`.zip`/`.iso` like a folder without extracting it first, including drag-and-drop copy of individual files out.
- **Built-in file manager** — a dual-pane view for browsing archives and the filesystem side by side.
- **Checksum calculation (CRC/SHA)** — right-click any file to compute a checksum, useful for verifying downloads.

---

## 7-Zip vs. Other Archivers

| | 7-Zip (.7z) | zip/unzip (.zip) | WinRAR (.rar) | tar + gzip (.tar.gz) |
|---|---|---|---|---|
| Compression ratio | Generally best | Moderate | Good | Moderate |
| Speed | Slower at high levels | Fast | Moderate | Fast |
| Native OS support | Needs a tool installed | Native on Windows/macOS | Needs a tool installed | Native on Linux/macOS |
| Cost | Free, open-source | Free | Paid (with trial) | Free, open-source |
| Encryption strength | Strong (AES-256) | Weak (ZipCrypto) or AES if supported | Strong (AES-256) | None built-in |
| Can create .rar | No (extract-only) | No | Yes | No |

---

## Example Walkthrough

```bash
7z a -tzip release.zip build/
7z l release.zip
7z x release.zip -o./extracted
```

Packages the `build` folder into a `.zip`, lists its contents, then extracts it into a new `extracted` folder while preserving the original structure.

```bash
7z a -t7z -mx9 -mhe=on -p backup.7z documents/
7z t backup.7z
```

Creates a maximally compressed, fully encrypted (contents and filenames) `.7z` backup of a documents folder, then verifies the resulting archive isn't corrupted before trusting it as a backup.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)