[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# find

`find` searches a directory tree for files and directories matching criteria you specify — by name, type, size, modification time, permissions, and more — and can run actions on whatever it finds.

Download: [https://man7.org/linux/man-pages/man1/find.1.html](https://man7.org/linux/man-pages/man1/find.1.html)

---

## What Is find?

Unlike `locate`, which searches a pre-built index and can be stale, `find` walks the actual filesystem in real time starting from a given path, checking every file and directory against the tests you provide. This makes it slower on huge filesystems but always accurate, and it means `find` can filter on live attributes like current permissions or exact modification time — not just names.

---

## Core Commands

```bash
find . -name "*.txt"                 # Find files by name pattern in the current dir and below
find /var/log -type f -mtime -7      # Files modified in the last 7 days
find . -type d -empty                # Find empty directories
find . -size +100M                   # Files larger than 100MB
find . -iname "readme*"              # Case-insensitive name search
find . -name "*.log" -delete         # Find and delete matching files
find . -name "*.sh" -exec chmod +x {} \;  # Run a command on every match
```

---

## Matching by Name and Type

| Test | Meaning |
|---|---|
| `-name pattern` | Match filename (case-sensitive), supports shell wildcards |
| `-iname pattern` | Case-insensitive name match |
| `-type f` | Regular files only |
| `-type d` | Directories only |
| `-type l` | Symbolic links only |
| `-path pattern` | Match against the full path, not just the filename |
| `-regex pattern` | Match the whole path against a regular expression |

---

## Matching by Time and Size

```bash
find . -mtime -1          # Modified in the last 1 day
find . -mtime +30         # Modified more than 30 days ago
find . -newer reference.txt  # Modified more recently than a reference file
find . -size +1G          # Larger than 1 gigabyte
find . -size -1k          # Smaller than 1 kilobyte
find . -empty             # Zero-byte files or empty directories
```

`-mtime` counts in whole 24-hour periods; `-mmin` gives minute-level precision (`-mmin -60` for "modified in the last hour") when day-level granularity isn't precise enough.

---

## Matching by Permissions and Ownership

```bash
find / -perm 4000                # Files with the setuid bit set
find . -user alice                # Files owned by a specific user
find . -group staff               # Files owned by a specific group
find / -perm -o+w -type f         # World-writable files (a common security audit)
```

---

## Running Actions on Results

```bash
find . -name "*.tmp" -delete
find . -name "*.jpg" -exec mv {} photos/ \;
find . -name "*.py" -exec grep -l "TODO" {} \;
find . -name "*.log" -print0 | xargs -0 rm
```

`-exec command {} \;` substitutes each match for `{}` and runs the command once per file — the trailing `\;` is required to terminate the `-exec` clause. `-exec ... +` batches many matches into fewer command invocations for better performance, similar to `xargs`. Using `-print0` with `xargs -0` (null-separated instead of newline-separated) safely handles filenames containing spaces or special characters, which plain `-exec` with a naive pipe to `xargs` can mishandle.

---

## Combining Conditions with Logical Operators

```bash
find . -name "*.txt" -o -name "*.md"       # OR: match either pattern
find . -type f -name "*.log" -a -size +10M # AND (implicit by default; -a is explicit)
find . -not -name "*.txt"                   # NOT: exclude a pattern
find . \( -name "*.tmp" -o -name "*.bak" \) -delete  # Grouped conditions
```

Parentheses (escaped as `\(` `\)` in the shell) group conditions so `-o` and `-a` combine the way you intend rather than left-to-right by default precedence.

---

## Limiting Search Depth

```bash
find . -maxdepth 1 -name "*.conf"    # Only the current directory, not subdirectories
find . -mindepth 2 -type f           # Skip the top two levels
```

---

## Common Gotchas

- Quoting patterns: `find . -name *.txt` without quotes lets the shell expand `*.txt` before `find` ever sees it, which can silently limit or break the search — always quote glob patterns passed to `find`.
- Performance on network filesystems: `find` traverses every directory, which can be slow over NFS or similarly latency-heavy mounts; `-maxdepth` or narrowing the starting path helps.

---

## Example Walkthrough

```bash
find . -type f -name "*.log" -mtime +30 -size +10M -exec rm {} \;
```

Finds and deletes log files that are both older than 30 days and larger than 10MB — a common disk-cleanup pattern combining several tests in one command.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
EOF

cat > "/home/claude/work/File Management/head.md" << 'EOF'
[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# head

`head` prints the first lines of one or more files, defaulting to the first 10 lines.

Download: [https://man7.org/linux/man-pages/man1/head.1.html](https://man7.org/linux/man-pages/man1/head.1.html)

---

## What Is head?

`head` reads a stream (a file or stdin) and stops after a fixed number of lines or bytes, without ever needing to read the rest of the file. This makes it a cheap way to preview large files, since `head` on a 10GB file returns instantly instead of scanning the whole thing.

---

## Core Commands

```bash
head file.txt                # Print the first 10 lines (default)
head -n 20 file.txt           # Print the first 20 lines
head -n -5 file.txt           # Print all lines except the last 5
head -c 100 file.txt          # Print the first 100 bytes
head -q file1.txt file2.txt   # Suppress filename headers when given multiple files
head file1.txt file2.txt      # Print first 10 lines of each, with filename headers
```

---

## All Major Options

| Flag | Meaning |
|---|---|
| `-n N` | Show the first N lines |
| `-n -N` | Show all but the last N lines |
| `-c N` | Show the first N bytes instead of lines |
| `-q` | Quiet — never print filename headers |
| `-v` | Verbose — always print filename headers, even for a single file |

---

## Using head in Pipelines

```bash
ps aux --sort=-%mem | head -5     # Top 5 memory-consuming processes
history | head -20                # First 20 commands from shell history
cat access.log | head -1          # Just the very first log line
```

`head` is frequently paired with a sorted or filtered stream to answer "what are the top N?" questions without writing the sort or filter output to a file first.

---

## Previewing Large or Unknown Files

```bash
head -c 200 unknown_file.bin | xxd
```

Combining `head -c` with a hex dump tool is a quick, safe way to peek at the start of a binary or unfamiliar file to identify its format before deciding how to process it further.

---

## Common Gotchas

- `head -n -5` (negative count) removes from the end rather than showing a fixed number from the start — easy to misread at a glance as "first negative-five lines," which doesn't mean what it looks like.
- Multiple files: without `-q`, `head` labels each file's output with a `==> filename <==` header, which can confuse a pipeline expecting plain lines — pass `-q` when piping multi-file output onward.

---

## Example Walkthrough

```bash
head -n 5 access.log
tail -n 5 access.log
```

Peeking at the first and last few lines of a log file is a fast way to sanity-check its format and time range before processing the whole thing.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
EOF

cat > "/home/claude/work/File Management/tail.md" << 'EOF'
[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# tail

`tail` prints the last lines of one or more files, defaulting to the last 10 lines, and can also follow a file live as new lines are appended.

Download: [https://man7.org/linux/man-pages/man1/tail.1.html](https://man7.org/linux/man-pages/man1/tail.1.html)

---

## What Is tail?

`tail` is `head`'s counterpart, reading from the end of a file rather than the start. Its most important feature beyond that is `-f` ("follow"), which keeps the command running and prints new lines as they're written to the file — making it the standard way to watch a log file in real time.

---

## Core Commands

```bash
tail file.txt                 # Print the last 10 lines (default)
tail -n 20 file.txt            # Print the last 20 lines
tail -n +5 file.txt            # Print from line 5 onward to the end
tail -f server.log             # Follow the file, printing new lines as they arrive
tail -F server.log             # Follow, and reattach if the file is rotated/recreated
tail -c 100 file.txt           # Print the last 100 bytes
```

---

## All Major Options

| Flag | Meaning |
|---|---|
| `-n N` | Show the last N lines |
| `-n +N` | Show from line N to the end |
| `-c N` | Show the last N bytes |
| `-f` | Follow — keep printing new lines as they're written |
| `-F` | Follow, but also handle log rotation (file renamed/recreated) |
| `-q` | Suppress filenames when given multiple files |
| `--pid=PID` | Used with `-f` to stop following once the given process exits |

---

## Following Logs in Real Time

```bash
tail -f /var/log/syslog
tail -f app.log | grep "ERROR"
```

`-f` is the default choice for watching an active log during debugging. Piping through `grep` while following lets you watch only the lines that match a pattern (like `ERROR`), rather than the full firehose of log output.

---

## Handling Log Rotation

```bash
tail -F app.log
```

Many logging setups periodically rename the current log file and start a new one (log rotation). Plain `-f` keeps watching the original file descriptor, which stops receiving new data once the file is rotated away. `-F` detects this and switches to following the new file under the same name — the better choice for any long-running "watch this log" session.

---

## Watching Multiple Files at Once

```bash
tail -f app.log error.log
```

`tail -f` with multiple files interleaves their output, printing a `==> filename <==` header whenever it switches which file's new lines it's showing — handy for watching two related services simultaneously.

---

## Stopping Automatically

```bash
tail --pid=1234 -f output.log
```

Combined with `-f`, `--pid` makes `tail` exit automatically once the specified process ID terminates, which is useful in scripts that start a background process and want to stream its log only while it's running.

---

## Common Gotchas

- `-f` vs `-F`: using plain `-f` on a log file managed by `logrotate` will silently stop showing new lines after rotation — this is one of the most common "why did my log watcher just stop updating" issues.
- Buffering: some programs buffer their output before writing to a log file, which can make `tail -f` appear to lag behind real events even though `tail` itself is working correctly.

---

## Example Walkthrough

```bash
tail -F -n 50 /var/log/nginx/error.log
```

Shows the last 50 lines of an Nginx error log and then keeps following it live, automatically reattaching if the log file gets rotated — a typical way to watch a production web server's errors.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)