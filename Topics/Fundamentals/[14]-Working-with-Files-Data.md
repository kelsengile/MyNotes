[Previous](./[13]-Text-Processing.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[15]-Networking-Web-Basics.md)

# Lesson 14 - Working with Files & Data

## 14.1 Reading and Writing Files

Almost every program eventually needs to read input from, or write output to, the filesystem. Handling this correctly means managing file handles, encodings, and errors carefully.

### Opening and Closing Files

Files are opened as a "handle" or "stream," used, and then must be closed to release the underlying OS resource. Failing to close files can leak file descriptors and, for writes, may leave data unflushed to disk.

```python
# Python — manual close (error-prone: skipped if an exception occurs)
f = open("data.txt", "r")
content = f.read()
f.close()

# Python — context manager (preferred: closes automatically, even on error)
with open("data.txt", "r") as f:
    content = f.read()
```

```javascript
// Node.js
const fs = require("fs");

// Synchronous
const content = fs.readFileSync("data.txt", "utf-8");

// Asynchronous (callback)
fs.readFile("data.txt", "utf-8", (err, data) => {
  if (err) throw err;
  console.log(data);
});

// Asynchronous (promises)
const fsp = require("fs/promises");
const content2 = await fsp.readFile("data.txt", "utf-8");
```

### File Modes

| Mode | Meaning |
|---|---|
| `r` | Read (file must exist) |
| `w` | Write (creates file, **overwrites** if it exists) |
| `a` | Append (creates file if missing, adds to the end) |
| `x` | Exclusive create (fails if file already exists) |
| `r+` | Read and write |
| `b` | Binary mode (e.g., `rb`, `wb`) — no text decoding |
| `t` | Text mode (default) — decodes to a string using an encoding |

### Reading Strategies

```python
with open("data.txt") as f:
    whole = f.read()          # entire file as one string
    
with open("data.txt") as f:
    lines = f.readlines()     # list of lines (keeps newline chars)

with open("data.txt") as f:
    for line in f:             # memory-efficient line-by-line iteration
        process(line.rstrip("\n"))
```

For large files, **stream/iterate rather than loading everything into memory at once** — this matters a great deal once files exceed available RAM (log files, large datasets, video).

```python
# Reading a large file in fixed-size chunks
with open("big.bin", "rb") as f:
    while chunk := f.read(8192):
        process(chunk)
```

### Writing Files

```python
with open("out.txt", "w") as f:
    f.write("Hello\n")
    f.writelines(["line1\n", "line2\n"])

with open("out.txt", "a") as f:   # append, don't overwrite
    f.write("more data\n")
```

### Binary vs. Text Mode

- **Text mode** decodes bytes to a string using a specified encoding (default is often platform-dependent — always specify explicitly, e.g. `encoding="utf-8"`) and handles newline translation across OSes.
- **Binary mode** (`"rb"`, `"wb"`) reads/writes raw bytes with no decoding — required for images, executables, compressed files, or any non-text data.

### Working with Paths

Use platform-independent path libraries rather than hardcoding `/` or `\`:

```python
from pathlib import Path

p = Path("data") / "2026" / "report.txt"
p.parent.mkdir(parents=True, exist_ok=True)
if p.exists():
    text = p.read_text(encoding="utf-8")
```

```javascript
const path = require("path");
const filePath = path.join("data", "2026", "report.txt");
```

### Error Handling for File I/O

Common failure modes to anticipate: file not found, permission denied, disk full, file locked by another process, or corrupted/partial reads.

```python
try:
    with open("config.json") as f:
        data = f.read()
except FileNotFoundError:
    print("Config file is missing.")
except PermissionError:
    print("No permission to read the file.")
```

### Best Practices

- Always close files (use context managers / `try`-`finally` / `using` blocks) so handles are released even if an error occurs.
- Be explicit about text encoding (`utf-8` is the safest default) to avoid cross-platform decoding issues.
- Use buffered/streamed reads for large files instead of loading everything into memory.
- Use atomic writes for critical data: write to a temporary file, then rename/move it into place, so a crash mid-write doesn't corrupt the original file.
- Watch out for newline differences across operating systems (`\n` vs `\r\n`); most languages' text mode handles this automatically, but binary mode does not.

---

## 14.2 Working with JSON/CSV/XML

These are the three most common structured/semi-structured text formats for exchanging data between systems.

### JSON (JavaScript Object Notation)

Lightweight, human-readable, and maps naturally onto nested objects/arrays — the de facto standard for web APIs and config files.

```json
{
  "name": "Alice",
  "age": 30,
  "active": true,
  "tags": ["admin", "user"],
  "address": { "city": "Manila", "zip": "1000" },
  "notes": null
}
```

```python
import json

# Parse a JSON string into Python objects (dict/list)
data = json.loads('{"name": "Alice", "age": 30}')

# Read directly from a file
with open("data.json") as f:
    data = json.load(f)

# Convert Python objects back to a JSON string
text = json.dumps(data, indent=2)

# Write directly to a file
with open("out.json", "w") as f:
    json.dump(data, f, indent=2)
```

```javascript
// JavaScript
const obj = JSON.parse('{"name": "Alice", "age": 30}');
const text = JSON.stringify(obj, null, 2);  // pretty-printed
```

**Notes:**
- JSON types map to: object → dict, array → list, string, number, boolean, `null`.
- JSON has no native support for dates, so timestamps are usually stored as ISO 8601 strings (`"2026-07-18T10:00:00Z"`) and parsed manually.
- **JSON Schema** can be used to validate a JSON document's structure programmatically.
- **JSON Lines (`.jsonl`)** stores one JSON object per line — useful for streaming large datasets without loading the whole file into memory.

### CSV (Comma-Separated Values)

A simple tabular format: rows are lines, columns are separated by a delimiter (usually a comma). Ubiquitous for spreadsheet exports and data interchange.

```csv
name,age,city
Alice,30,Manila
"Smith, Bob",25,Cebu
```

```python
import csv

# Reading
with open("data.csv", newline="") as f:
    reader = csv.reader(f)
    header = next(reader)
    for row in reader:
        print(row)   # row is a list of strings

# Reading into dictionaries (uses the header row as keys)
with open("data.csv", newline="") as f:
    reader = csv.DictReader(f)
    for row in reader:
        print(row["name"], row["age"])

# Writing
with open("out.csv", "w", newline="") as f:
    writer = csv.writer(f)
    writer.writerow(["name", "age"])
    writer.writerow(["Alice", 30])
```

**Pitfalls to avoid:**
- **Never split on `,` manually** — fields can contain commas if quoted (e.g., `"Smith, Bob"`), and a naive split will break. Always use a CSV library.
- All values are read as strings by default — numbers, booleans, and dates must be explicitly converted.
- Watch for inconsistent delimiters (`;` is common in some locales), inconsistent quoting, and encoding issues (BOM markers in Excel-exported files).
- Always pass `newline=""` when opening CSV files in Python to prevent extra blank lines on Windows.

### XML (eXtensible Markup Language)

A verbose, tag-based hierarchical format, still common in enterprise systems, legacy APIs, and configuration formats (e.g., Android layouts, SOAP APIs).

```xml
<?xml version="1.0" encoding="UTF-8"?>
<person id="1">
  <name>Alice</name>
  <age>30</age>
  <tags>
    <tag>admin</tag>
    <tag>user</tag>
  </tags>
</person>
```

```python
import xml.etree.ElementTree as ET

tree = ET.parse("data.xml")
root = tree.getroot()

name = root.find("name").text
person_id = root.get("id")            # reading an attribute

for tag in root.find("tags").findall("tag"):
    print(tag.text)

# Building and writing XML
new_root = ET.Element("person", id="2")
ET.SubElement(new_root, "name").text = "Bob"
ET.ElementTree(new_root).write("out.xml")
```

**Concepts:**
- **Elements** (tags), **attributes** (`id="1"`), and **text content** are the three ways XML stores data.
- **XPath** is a query language for navigating XML documents (`//person/name`).
- **Namespaces** (`xmlns`) prevent tag name collisions when combining XML vocabularies from different sources.
- **Schemas** (XSD, DTD) define and validate the expected structure of an XML document.

### Comparing the Three

| | JSON | CSV | XML |
|---|---|---|---|
| Structure | Nested (objects/arrays) | Flat/tabular only | Nested, with attributes |
| Readability | High | High (for simple data) | Lower (verbose) |
| Typed values | Yes (string, number, bool, null) | No (everything is a string) | No (everything is text/attributes) |
| Comments | Not supported | Not supported | Supported (`<!-- -->`) |
| Best for | APIs, config, nested data | Spreadsheets, tabular exports | Legacy/enterprise systems, documents |
| File size | Moderate | Compact | Verbose |

---

## 14.3 Databases (SQL Basics, NoSQL Intro)

### SQL (Relational Databases)

Relational databases (PostgreSQL, MySQL, SQLite, SQL Server, Oracle) store data in **tables** made of rows and columns, with relationships enforced between tables via keys.

**Core concepts:**
- **Table** — a collection of rows with a fixed set of typed columns.
- **Primary key** — a column (or set of columns) that uniquely identifies each row.
- **Foreign key** — a column referencing a primary key in another table, establishing a relationship.
- **Schema** — the defined structure of tables, columns, types, and constraints.
- **Normalization** — organizing tables to reduce data duplication and improve integrity (typically up to 3rd normal form in practice).

**Basic queries:**

```sql
-- Create a table
CREATE TABLE users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    email TEXT UNIQUE,
    age INTEGER
);

-- Insert data
INSERT INTO users (name, email, age) VALUES ('Alice', 'alice@example.com', 30);

-- Query data
SELECT name, age FROM users WHERE age > 25 ORDER BY age DESC;

-- Update data
UPDATE users SET age = 31 WHERE name = 'Alice';

-- Delete data
DELETE FROM users WHERE id = 5;

-- Join across tables
SELECT orders.id, users.name, orders.total
FROM orders
JOIN users ON orders.user_id = users.id
WHERE orders.total > 100;

-- Aggregate functions
SELECT city, COUNT(*) AS num_users, AVG(age) AS avg_age
FROM users
GROUP BY city
HAVING COUNT(*) > 10;
```

**Types of joins:**

| Join | Returns |
|---|---|
| `INNER JOIN` | Only rows with matches in both tables |
| `LEFT JOIN` | All rows from the left table, matched rows from the right (NULL if no match) |
| `RIGHT JOIN` | All rows from the right table, matched rows from the left |
| `FULL OUTER JOIN` | All rows from both tables, matched where possible |

**Transactions and ACID:**
A transaction groups multiple operations so they succeed or fail as a single unit — critical for operations like transferring money between accounts.

```sql
BEGIN TRANSACTION;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;   -- or ROLLBACK on failure
```

- **A**tomicity — all operations in a transaction succeed, or none do.
- **C**onsistency — the database moves from one valid state to another, respecting all constraints.
- **I**solation — concurrent transactions don't interfere with each other's intermediate states.
- **D**urability — once committed, changes survive crashes/power loss.

**Using SQL from application code (parameterized queries):**

```python
import sqlite3

conn = sqlite3.connect("app.db")
cur = conn.cursor()

# NEVER use string formatting/concatenation for values — it enables SQL injection
# BAD:  cur.execute(f"SELECT * FROM users WHERE name = '{name}'")

# GOOD: use parameterized queries
cur.execute("SELECT * FROM users WHERE name = ?", (name,))
rows = cur.fetchall()
conn.commit()
conn.close()
```

**Indexes** speed up lookups on frequently queried columns at the cost of extra storage and slower writes:
```sql
CREATE INDEX idx_users_email ON users(email);
```

### NoSQL (Non-Relational Databases)

NoSQL databases trade some of the strict structure and consistency guarantees of SQL for flexibility and horizontal scalability. They're grouped into several categories:

| Type | Description | Examples |
|---|---|---|
| **Document stores** | Store semi-structured documents (usually JSON/BSON), each with its own flexible schema | MongoDB, Couchbase, Firestore |
| **Key-value stores** | Simple key → value lookups, extremely fast | Redis, DynamoDB, Memcached |
| **Column-family stores** | Optimized for writing/reading large volumes of columnar data | Cassandra, HBase |
| **Graph databases** | Model data as nodes and relationships/edges, ideal for highly connected data | Neo4j, Amazon Neptune |

**Example — document store (MongoDB):**

```javascript
// Insert a document (no rigid schema required)
db.users.insertOne({
  name: "Alice",
  age: 30,
  tags: ["admin", "user"],
  address: { city: "Manila" }
});

// Query
db.users.find({ age: { $gt: 25 } });

// Update
db.users.updateOne({ name: "Alice" }, { $set: { age: 31 } });
```

**Example — key-value store (Redis):**

```
SET session:abc123 "user_id=42"
GET session:abc123
EXPIRE session:abc123 3600     # auto-delete after 1 hour
```

### SQL vs. NoSQL — When to Use Which

| | SQL (Relational) | NoSQL |
|---|---|---|
| Schema | Fixed, defined upfront | Flexible/dynamic |
| Relationships | Strong (joins, foreign keys) | Usually denormalized/embedded |
| Consistency | Strong (ACID) by default | Often "eventual consistency" (varies by system) |
| Scaling | Primarily vertical (though modern systems support horizontal too) | Designed for horizontal scaling |
| Best for | Structured data with complex relationships, transactions (finance, inventory) | Rapidly evolving schemas, huge volumes of semi-structured data, caching, real-time apps |
| Query language | SQL (standardized) | Varies by database (no universal standard) |

Many real-world systems use **both**: a relational database for core transactional data, and a NoSQL store (like Redis) for caching or session data, and/or a document store for flexible, rapidly changing data models.

### General Best Practices

- Always use parameterized queries/prepared statements — never build SQL by concatenating user input, to prevent SQL injection.
- Design indexes around your actual query patterns, not speculatively — unused indexes slow down writes for no benefit.
- Understand your consistency requirements before choosing NoSQL — "eventual consistency" is fine for a social media feed but risky for a bank balance.
- Use migrations (versioned scripts) to evolve database schemas in a tracked, repeatable way rather than making ad hoc changes.
- Back up data regularly and test restoring from backups — a backup you've never restored is not a reliable backup.

[Previous](./[13]-Text-Processing.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[15]-Networking-Web-Basics.md)
