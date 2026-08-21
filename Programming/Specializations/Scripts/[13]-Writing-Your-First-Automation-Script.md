[Previous](./[12]-Regular-Expressions.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[14]-Scheduling-Scripts.md)

*Automation Basics*

# Lesson 13 - Writing Your First Automation Script

## 13.1 Planning the Script

Before writing code, define:
- **Input**: what does the script need (files, arguments, environment)?
- **Process**: what steps transform the input?
- **Output**: what does success look like?

A simple example: a script that backs up a folder to a timestamped archive.

---

## 13.2 A Complete Example

```bash
#!/bin/bash
set -euo pipefail

SOURCE_DIR="$1"
BACKUP_DIR="./backups"
TIMESTAMP=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/backup_$TIMESTAMP.tar.gz" "$SOURCE_DIR"

echo "Backup created: $BACKUP_DIR/backup_$TIMESTAMP.tar.gz"
```

`set -euo pipefail` makes the script exit on errors, treat unset variables as errors, and fail if any command in a pipeline fails — a strong default for automation scripts (covered more in Lesson 16).

---

## 13.3 Testing and Iterating

Run the script with realistic inputs, check the output, and handle edge cases (missing arguments, nonexistent directories) before relying on it. Automation scripts should fail loudly and clearly rather than silently doing the wrong thing.

---

[Previous](./[12]-Regular-Expressions.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[14]-Scheduling-Scripts.md)
