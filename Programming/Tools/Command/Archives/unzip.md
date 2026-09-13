[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

# unzip

`unzip` extracts files from `.zip` archives. It's the counterpart to `zip`, and on most Linux distributions the two are installed together.

Download: [https://infozip.sourceforge.net/UnZip.html](https://infozip.sourceforge.net/UnZip.html)

---

## What Is unzip?

`unzip` reads the central directory at the end of a `.zip` file to figure out what's inside, then pulls out the files you ask for (or all of them, by default) — decompressing each one as it goes.

---

## Core Commands

```bash
unzip archive.zip                   # Extract everything into the current folder
unzip archive.zip -d /path/         # Extract into a specific directory
unzip -l archive.zip                # List contents without extracting
unzip -o archive.zip                # Overwrite existing files without prompting
unzip archive.zip "*.txt"           # Extract only files matching a pattern
unzip -t archive.zip                # Test the archive for corruption
```

---

## Handling Conflicts

By default, `unzip` asks before overwriting a file that already exists. Scripting an extraction? Use `-o` to overwrite silently, or `-n` to always keep the existing file and skip the one from the archive.

---

## Example Walkthrough

```bash
unzip -l dataset.zip
unzip dataset.zip -d ./data
ls ./data
```

Previews what's inside `dataset.zip`, extracts it into a dedicated `data` folder, then confirms the files landed where expected.

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
