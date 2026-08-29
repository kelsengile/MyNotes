[Previous](./[24]-Importing-and-Exporting-Data.md) | [Table of Contents](./%5B0%5D-Introduction-to-SQL.md) | [Next](./[26]-Query-Optimization.md)

# Lesson 25 - SQL Dialects — PostgreSQL, MySQL, SQLite & SQL Server


---

Throughout this course, dialect notes have flagged differences as they came up. This lesson consolidates them into one reference, and introduces each database's overall character and best use cases.

---

## The four databases at a glance

| | PostgreSQL | MySQL | SQLite | SQL Server |
|---|---|---|---|---|
| Type | Server-based | Server-based | Embedded/file-based | Server-based |
| License | Open source | Open source (with a paid enterprise tier) | Public domain | Commercial (Microsoft) |
| Best known for | Standards compliance, extensibility, advanced features | Speed, huge ecosystem, web hosting ubiquity | Zero-config, embedded use, mobile apps | Enterprise/Windows integration, tooling |
| Typical use case | Complex applications, analytics, geospatial data | Web applications, WordPress, general-purpose apps | Mobile apps, local caches, small tools, prototyping | Enterprise software, .NET applications |

---

## Where dialects diverge — a consolidated reference

### Limiting results
```sql
-- PostgreSQL, MySQL, SQLite
SELECT * FROM books LIMIT 5 OFFSET 10;

-- SQL Server (older, still common)
SELECT TOP 5 * FROM books;

-- SQL Server (standard-compliant alternative)
SELECT * FROM books ORDER BY book_id OFFSET 10 ROWS FETCH NEXT 5 ROWS ONLY;
```

### Auto-incrementing primary keys
```sql
-- PostgreSQL
id SERIAL PRIMARY KEY;               -- or: GENERATED ALWAYS AS IDENTITY

-- MySQL
id INT AUTO_INCREMENT PRIMARY KEY;

-- SQLite
id INTEGER PRIMARY KEY;              -- auto-increments by default

-- SQL Server
id INT IDENTITY(1,1) PRIMARY KEY;
```

### String concatenation
```sql
-- PostgreSQL, SQLite
SELECT first_name || ' ' || last_name FROM people;

-- MySQL
SELECT CONCAT(first_name, ' ', last_name) FROM people;

-- SQL Server
SELECT first_name + ' ' + last_name FROM people;
-- or, also supported:
SELECT CONCAT(first_name, ' ', last_name) FROM people;
```

### Getting the current date/time
```sql
-- Standard (works in PostgreSQL, MySQL, SQL Server)
SELECT CURRENT_DATE, CURRENT_TIMESTAMP;

-- SQLite
SELECT DATE('now'), DATETIME('now');
```

### Case sensitivity of identifiers
- PostgreSQL: unquoted identifiers are folded to lowercase; quoted identifiers (`"MyTable"`) preserve case
- MySQL: table name case-sensitivity depends on the underlying OS filesystem (case-sensitive on Linux, insensitive on Windows/macOS by default)
- SQLite: generally case-insensitive for ASCII, with some documented quirks
- SQL Server: depends on the database's collation setting, but is commonly case-insensitive by default

### RIGHT JOIN and FULL JOIN support
- PostgreSQL, MySQL, SQL Server: full support for both
- SQLite: `RIGHT JOIN` only since v3.39 (2022); `FULL JOIN` unsupported — simulate with `UNION` of a `LEFT JOIN` and its mirror

### INTERSECT / EXCEPT
- PostgreSQL, SQLite, SQL Server: fully supported
- MySQL: added only in 8.0.31+ (2022); earlier versions require workarounds using joins or `NOT IN`

### Stored procedures/functions and triggers
- PostgreSQL: PL/pgSQL (and other pluggable languages)
- MySQL: its own procedural extension
- SQL Server: T-SQL
- SQLite: **no stored procedures or functions**; simplified trigger syntax only

### Boolean type
- PostgreSQL: native `BOOLEAN` type (`TRUE`/`FALSE`)
- MySQL: `BOOLEAN` is an alias for `TINYINT(1)` — stores `0`/`1`
- SQLite: no true boolean type; stores `0`/`1` as integers
- SQL Server: `BIT` type, storing `0`/`1`

### JSON support
- PostgreSQL: rich native support via `JSON` and (better) `JSONB`, with a full set of operators and functions
- MySQL: native `JSON` type since 5.7, with `JSON_EXTRACT` and related functions
- SQLite: JSON functions available as a built-in extension (`json_extract`, etc.)
- SQL Server: `JSON` stored as text with `OPENJSON`/`JSON_VALUE` functions, no dedicated JSON storage type

---

## Why does this variation exist?

SQL is governed by an ANSI/ISO standard, but the standard leaves many behaviors unspecified or optional, and vendors have historically added proprietary extensions before the standard caught up (or instead of ever adopting it). The core `SELECT`/`FROM`/`WHERE`/`JOIN`/`GROUP BY` grammar you learned in earlier lessons is close to universal — most divergence shows up in edge cases, procedural extensions, and administrative commands.

---

## Practical advice for working across dialects

- Stick to standard, portable syntax (plain joins, `WHERE`, `GROUP BY`, standard functions) whenever a query doesn't specifically need a vendor feature — this keeps code portable if you ever migrate databases
- When you must use a vendor-specific feature (like PostgreSQL's `JSONB` or SQL Server's `MERGE`), document that dependency clearly
- Always check the specific documentation for your database version when working with dates, string functions, or anything beyond the SQL basics — assuming another dialect's behavior is one of the most common sources of bugs when switching databases
- ORMs (Object-Relational Mappers) in application frameworks can abstract away some — but rarely all — of these differences; complex queries often still need dialect-specific tuning

---

## Choosing a database for a new project (a rough guide)

- **Prototyping, mobile apps, small local tools** → SQLite
- **General-purpose web applications, cost-sensitive projects** → MySQL or PostgreSQL
- **Complex queries, analytics, geospatial or JSON-heavy data, standards compliance** → PostgreSQL
- **Enterprise environments already invested in Microsoft's ecosystem** → SQL Server

---

[Previous](./[24]-Importing-and-Exporting-Data.md) | [Table of Contents](./%5B0%5D-Introduction-to-SQL.md) | [Next](./[26]-Query-Optimization.md)
