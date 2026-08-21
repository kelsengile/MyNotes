[Previous](./[17]-Python-Basics-for-Scripting.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[19]-Automating-Tasks-with-Python.md)

*Python Scripting*

# Lesson 18 - Working With Files & The Filesystem In Python

## 18.1 Reading and Writing Files

```python
with open("file.txt", "r") as f:
    contents = f.read()

with open("output.txt", "w") as f:
    f.write("Hello, file!\n")

with open("file.txt") as f:
    for line in f:
        print(line.strip())
```

Using `with` ensures the file is closed automatically, even if an error occurs.

---

## 18.2 pathlib

`pathlib` is the modern way to work with file paths in Python:

```python
from pathlib import Path

p = Path("data") / "input.csv"
print(p.exists())
print(p.name, p.suffix, p.parent)

for file in Path(".").glob("*.txt"):
    print(file)
```

---

## 18.3 Common Filesystem Operations

```python
import shutil, os

os.makedirs("new_dir", exist_ok=True)
shutil.copy("a.txt", "b.txt")
shutil.move("b.txt", "archive/b.txt")
os.remove("a.txt")
shutil.rmtree("old_dir")
```

---

[Previous](./[17]-Python-Basics-for-Scripting.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[19]-Automating-Tasks-with-Python.md)
