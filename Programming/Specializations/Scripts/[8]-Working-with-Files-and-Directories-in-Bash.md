[Previous](./[7]-Functions-in-Bash.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[9]-Standard-Input-Output-and-Redirection.md)

*Shell Scripting Fundamentals*

# Lesson 8 - Working With Files And Directories In Bash

## 8.1 Testing Files and Directories

```bash
if [ -f "file.txt" ]; then
    echo "File exists"
fi

if [ -d "folder" ]; then
    echo "Directory exists"
fi
```

Other useful tests: `-r` (readable), `-w` (writable), `-x` (executable), `-s` (non-empty).

---

## 8.2 Reading Files Line by Line

```bash
while IFS= read -r line; do
    echo "Line: $line"
done < "input.txt"
```

`IFS=` prevents leading/trailing whitespace from being stripped, and `-r` prevents backslashes from being interpreted.

---

## 8.3 Looping Over Directory Contents

```bash
for entry in /path/to/dir/*; do
    if [ -d "$entry" ]; then
        echo "Dir: $entry"
    else
        echo "File: $entry"
    fi
done
```

---

## 8.4 File Permissions

```bash
chmod 755 script.sh     # rwxr-xr-x
chmod u+x script.sh     # add execute for the owner
chown user:group file.txt
```

Permissions are essential when writing scripts meant to run as scheduled jobs or be shared with others — an unreadable or non-executable script will fail silently in automation contexts.

---

[Previous](./[7]-Functions-in-Bash.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[9]-Standard-Input-Output-and-Redirection.md)
