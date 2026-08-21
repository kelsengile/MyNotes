[Previous](./[37]-Writing-Maintainable-and-Readable-Scripts.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[39]-Performance-Considerations-in-Scripting.md)

*Best Practices*

# Lesson 38 - Testing Scripts

## 38.1 Why Test Scripts?

Automation scripts often run unattended against real systems — a silent bug can cause data loss or downtime. Testing catches mistakes before they run in production.

---

## 38.2 Manual and Static Testing

- **Dry runs**: add a `--dry-run` flag that prints what the script *would* do without doing it.
- **Linting**: `shellcheck script.sh` catches common Bash mistakes; `pylint`/`ruff` do the same for Python.

```bash
if [ "$DRY_RUN" = true ]; then
    echo "Would delete: $file"
else
    rm "$file"
fi
```

---

## 38.3 Automated Testing in Python

```python
# backup.py
def get_backup_filename(date):
    return f"backup_{date}.tar.gz"

# test_backup.py
from backup import get_backup_filename

def test_get_backup_filename():
    assert get_backup_filename("20250101") == "backup_20250101.tar.gz"
```

Run with `pytest`. Separating logic into testable functions (rather than one long script) makes automated testing possible.

---

## 38.4 Testing Bash Scripts

Tools like **Bats** (Bash Automated Testing System) let you write test cases for shell scripts:

```bash
@test "addition works" {
    result="$(add 2 3)"
    [ "$result" -eq 5 ]
}
```

---

## 38.5 Testing in a Safe Environment

Test destructive scripts (deleting files, modifying users, restarting services) against a disposable VM, container, or test directory before running them against real systems.

---

[Previous](./[37]-Writing-Maintainable-and-Readable-Scripts.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[39]-Performance-Considerations-in-Scripting.md)
