[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# tr (translate/transliterate)

`tr` reads standard input and replaces, deletes, or squeezes characters based on simple character-set rules. It doesn't work on files directly or understand lines — it's a character-level filter, one of the most minimal but surprisingly versatile tools in the classic Unix text-processing toolkit.

Reference: [https://man7.org/linux/man-pages/man1/tr.1.html](https://man7.org/linux/man-pages/man1/tr.1.html)

---

## What Is tr?

`tr` operates purely on a stream of bytes/characters from stdin, mapping each character in `SET1` to the corresponding character in `SET2` (or deleting/squeezing characters in `SET1` entirely, depending on flags). It has no concept of "fields," "lines," or "words" the way `awk` or `sed` does — it's deliberately the simplest possible character transformer, which is exactly why it's fast and composes so well in pipelines.

Because `tr` only reads stdin (it cannot take a filename argument the way `cat` or `grep` can), it's almost always used with a pipe or input redirection: `cat file.txt | tr ... ` or `tr ... < file.txt`.

---

## Core Commands

```bash
tr 'a-z' 'A-Z' < file.txt           # Convert lowercase to uppercase
echo "Hello" | tr 'a-z' 'A-Z'       # Same idea, from a pipe
tr -d '0-9' < file.txt              # Delete all digits
tr -s ' ' < file.txt                # Squeeze repeated spaces into a single space
tr -c 'a-zA-Z\n' ' ' < file.txt     # Replace everything EXCEPT letters and newlines with a space
tr '\n' ',' < file.txt              # Replace newlines with commas (joins lines)
tr -d '\r' < windows_file.txt       # Strip carriage returns (convert CRLF to LF)
tr -cd '[:print:]' < file.txt       # Keep only printable characters, delete everything else
```

## Full Option Reference

| Flag | Long form | Meaning |
|---|---|---|
| `-d` | `--delete` | Delete characters in SET1, don't translate |
| `-s` | `--squeeze-repeats` | Collapse consecutive repeats of translated characters into one |
| `-c`, `-C` | `--complement` | Operate on the complement of SET1 (everything not in it) |
| `-t` | `--truncate-set1` | Truncate SET1 to the length of SET2 |

## Character Classes tr Understands

`tr` supports POSIX character classes for cleaner rules than spelling out ranges:

| Class | Meaning |
|---|---|
| `[:upper:]` | Uppercase letters |
| `[:lower:]` | Lowercase letters |
| `[:digit:]` | Digits 0–9 |
| `[:alpha:]` | Letters |
| `[:alnum:]` | Letters and digits |
| `[:space:]` | Whitespace (space, tab, newline, etc.) |
| `[:punct:]` | Punctuation characters |
| `[:print:]` | Printable characters |

```bash
tr '[:lower:]' '[:upper:]' < file.txt   # Same effect as a-z A-Z, but locale-aware
```

---

## Squeeze, Delete, and Complement: The Three Superpowers

- **Delete (`-d`)** removes characters entirely rather than mapping them: `tr -d '[:punct:]'` strips all punctuation from a stream.
- **Squeeze (`-s`)** collapses runs of a repeated character into a single instance — extremely handy for cleaning up messy, inconsistently-spaced text: `tr -s ' '` turns multiple spaces into one.
- **Complement (`-c`)** flips the character set, letting you say "everything except these" instead of listing every character you care about — useful for stripping out anything non-alphanumeric in one line: `tr -cd '[:alnum:]\n'`.

Combining `-s` and `-d`, or `-c` and `-d`, lets `tr` do surprisingly sophisticated single-pass cleanup that would otherwise require a small script.

---

## Real-World Uses Beyond Case Conversion

- **Line-ending conversion**: `tr -d '\r'` strips the carriage returns from Windows-style CRLF line endings, converting to Unix LF.
- **Word-frequency counting**: a classic Unix one-liner, `tr -cs '[:alpha:]' '\n' < file.txt | sort | uniq -c | sort -rn`, splits text into one word per line (replacing every non-letter run with a newline) as the first step toward counting word frequency.
- **Sanitizing filenames or identifiers**: stripping or replacing characters that aren't safe in a given context.
- **ROT13 and simple substitution ciphers**: `tr 'A-Za-z' 'N-ZA-Mn-za-m'` implements ROT13 in a single command, since `tr` is fundamentally a character substitution cipher engine.
- **Counting characters**: with `-d` and `-c` combined with `wc -c`, you can count occurrences of a specific character class.

---

## Related Tools

- `sed` — line- and pattern-based substitution with regular expressions; use this when you need context-sensitive replacement, not just character mapping.
- `awk` — field-aware text processing, for anything involving columns or structured records.
- `iconv` — character *encoding* conversion (e.g., Latin-1 to UTF-8), a different problem from `tr`'s character *substitution*.
- `dos2unix` / `unix2dos` — dedicated tools for the line-ending conversion `tr -d '\r'` performs manually.

---

## Example Walkthrough

```bash
cat messy.txt | tr -s ' ' | tr -d '\r' | tr '[:lower:]' '[:upper:]'
```

Chains three `tr` calls to clean up a messy text file in one pipeline: collapsing repeated spaces, stripping stray carriage returns, and finally converting everything to uppercase — the kind of quick, composable cleanup `tr` excels at compared to writing a dedicated script.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)