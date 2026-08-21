[Previous](./[15]-Environment-Variables-and-Configuration-Files.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[17]-Python-Basics-for-Scripting.md)

*Automation Basics*

# Lesson 16 - Error Handling & Exit Codes

## 16.1 Exit Codes

Every command returns an **exit code**: `0` means success, any non-zero value means failure. Check it with `$?`:

```bash
ls /nonexistent
echo $?    # prints a non-zero code, e.g. 2
```

---

## 16.2 Bash Safety Flags

```bash
set -e            # exit immediately if a command fails
set -u            # treat unset variables as an error
set -o pipefail   # a pipeline fails if any command in it fails
set -euo pipefail # all three combined — a common script header
```

---

## 16.3 Handling Errors Explicitly

```bash
if ! cp "$SRC" "$DEST"; then
    echo "Error: copy failed" >&2
    exit 1
fi
```

Writing error messages to stderr (`>&2`) keeps them separate from normal output, which matters when a script's output is piped into another program.

---

## 16.4 Cleanup with trap

`trap` runs a command when the script exits, even on error — useful for cleaning up temporary files:

```bash
tmpfile=$(mktemp)
trap 'rm -f "$tmpfile"' EXIT

# ... use $tmpfile ...
```

---

[Previous](./[15]-Environment-Variables-and-Configuration-Files.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[17]-Python-Basics-for-Scripting.md)
