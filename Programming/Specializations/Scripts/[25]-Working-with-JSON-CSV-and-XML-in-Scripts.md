[Previous](./[24]-Argument-Parsing-and-Command-Line-Interfaces.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[26]-Logging-in-Scripts.md)

*Advanced Scripting Concepts*

# Lesson 25 - Working With JSON, CSV & XML In Scripts

## 25.1 JSON in Python

```python
import json

with open("data.json") as f:
    data = json.load(f)

data["new_key"] = "value"

with open("data.json", "w") as f:
    json.dump(data, f, indent=2)
```

---

## 25.2 JSON in Bash with jq

Bash has no native JSON support, so the `jq` tool is standard:

```bash
curl -s https://api.example.com/users | jq '.[0].name'
cat data.json | jq '.users[] | select(.active == true)'
```

---

## 25.3 CSV in Python

```python
import csv

with open("data.csv", newline="") as f:
    reader = csv.DictReader(f)
    for row in reader:
        print(row["name"], row["email"])

with open("out.csv", "w", newline="") as f:
    writer = csv.DictWriter(f, fieldnames=["name", "email"])
    writer.writeheader()
    writer.writerow({"name": "Alice", "email": "alice@example.com"})
```

---

## 25.4 XML in Python and Structured Data in PowerShell

```python
import xml.etree.ElementTree as ET

tree = ET.parse("data.xml")
root = tree.getroot()
for item in root.findall("item"):
    print(item.get("id"), item.text)
```

PowerShell has native cmdlets for structured data, no extra library needed:

```powershell
$json = Get-Content data.json | ConvertFrom-Json
$csv = Import-Csv data.csv
$xml = [xml](Get-Content data.xml)
```

---

[Previous](./[24]-Argument-Parsing-and-Command-Line-Interfaces.md) | [Table of Contents](./[0]-Introduction-to-Scripts.md) | [Next](./[26]-Logging-in-Scripts.md)
