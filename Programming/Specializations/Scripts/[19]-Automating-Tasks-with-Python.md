[Previous](./[18]-Working-with-Files-and-the-Filesystem-in-Python.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[20]-Working-with-APIs-and-HTTP-Requests-in-Scripts.md)

*Python Scripting*

# Lesson 19 - Automating Tasks With Python

## 19.1 Running External Commands

```python
import subprocess

result = subprocess.run(
    ["git", "status"],
    capture_output=True, text=True, check=True
)
print(result.stdout)
```

`check=True` raises an exception if the command exits with a non-zero status, which is usually what you want in automation.

---

## 19.2 A Practical Example: Renaming Files in Bulk

```python
from pathlib import Path

for file in Path("photos").glob("*.jpeg"):
    new_name = file.with_suffix(".jpg")
    file.rename(new_name)
    print(f"Renamed {file.name} -> {new_name.name}")
```

---

## 19.3 Scheduling Python Scripts

Python scripts are scheduled the same way as any other script — via `cron` on Linux/macOS or Task Scheduler on Windows — by calling the interpreter directly:

```
0 3 * * * /usr/bin/python3 /home/user/cleanup.py >> /home/user/cleanup.log 2>&1
```

---

## 19.4 Structuring Larger Automation Scripts

As a script grows, break logic into functions and guard the entry point:

```python
def main():
    # main logic here
    pass

if __name__ == "__main__":
    main()
```

This pattern lets a file be both run directly and imported by other scripts without side effects.

---

[Previous](./[18]-Working-with-Files-and-the-Filesystem-in-Python.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[20]-Working-with-APIs-and-HTTP-Requests-in-Scripts.md)
