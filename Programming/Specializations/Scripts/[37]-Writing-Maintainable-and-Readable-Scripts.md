[Previous](./[36]-Script-Security-Best-Practices.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[38]-Testing-Scripts.md)

*Best Practices*

# Lesson 37 - Writing Maintainable & Readable Scripts

## 37.1 Naming and Structure

- Use descriptive variable and function names (`backup_dir`, not `bd`).
- Keep functions focused on a single task.
- Group related logic together, and separate configuration from logic near the top of the script.

---

## 37.2 Comments and Documentation

```bash
#!/bin/bash
#
# backup.sh — Archives a directory and removes backups older than 30 days.
# Usage: ./backup.sh <source_dir>
```

Comment *why* something is done, not just *what* the code does — the "what" should be clear from readable code itself.

---

## 37.3 Consistent Style

- Pick a consistent indentation style and stick to it.
- Use a linter: `shellcheck` for Bash, `pylint`/`ruff` for Python, `PSScriptAnalyzer` for PowerShell.
- Keep line length reasonable so scripts are easy to read in a terminal.

---

## 37.4 Avoiding Duplication

If the same logic appears in more than two places, extract it into a function. In larger projects, extract shared functions into a separate file and `source` it:

```bash
source "./lib/common.sh"
```

```python
from mylib.helpers import backup_directory
```

Maintainable scripts are easier to debug, extend, and hand off to someone else later — including your future self.

---

[Previous](./[36]-Script-Security-Best-Practices.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[38]-Testing-Scripts.md)
