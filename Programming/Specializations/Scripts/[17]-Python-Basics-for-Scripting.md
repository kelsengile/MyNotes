[Previous](./[16]-Error-Handling-and-Exit-Codes.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[18]-Working-with-Files-and-the-Filesystem-in-Python.md)

*Python Scripting*

# Lesson 17 - Python Basics For Scripting

## 17.1 Why Python for Scripting

Python is readable, has a huge standard library, and runs the same way on Linux, macOS, and Windows, making it a strong choice once a task outgrows what's comfortable in Bash.

---

## 17.2 Running a Python Script

```python
#!/usr/bin/env python3
print("Hello, world!")
```

```bash
chmod +x script.py
./script.py
# or
python3 script.py
```

---

## 17.3 Core Syntax Recap

```python
name = "Alice"          # variables are dynamically typed
count = 5

if count > 0:
    print(f"{name} has {count} items")

for item in ["a", "b", "c"]:
    print(item)

def greet(person):
    return f"Hello, {person}!"
```

---

## 17.4 Common Standard Library Modules for Scripting

| Module | Purpose |
|---|---|
| `os` | interact with the operating system |
| `sys` | command-line arguments, exit codes |
| `subprocess` | run external commands |
| `shutil` | high-level file operations |
| `pathlib` | modern, object-oriented file paths |

```python
import subprocess
result = subprocess.run(["ls", "-la"], capture_output=True, text=True)
print(result.stdout)
```

---

[Previous](./[16]-Error-Handling-and-Exit-Codes.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[18]-Working-with-Files-and-the-Filesystem-in-Python.md)
