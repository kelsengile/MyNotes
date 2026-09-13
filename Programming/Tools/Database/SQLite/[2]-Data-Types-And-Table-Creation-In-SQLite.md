[Previous](./[1]-Getting-Started-With-SQLite.md) | [Table of Contents](./[0]-Introduction-to-SQLite.md) | [Next](./[3]-CRUD-And-Querying-In-SQLite.md)

*Data And Querying*

# Lesson 2 - Data Types And Table Creation In SQLite

## 2.1 Dynamic Typing And Type Affinity

Most relational databases use **static typing**: a column declared `VARCHAR(50)` will reject a number, and a column declared `INTEGER` will reject text. SQLite works differently — it uses **dynamic typing**, meaning the type actually stored is a property of the individual *value*, not strictly enforced by the column's declared type. In practice, a normal SQLite column will happily store a text string even if it was declared `INTEGER`.

Instead, every column has a **type affinity** — a preference that SQLite uses to decide how to store an incoming value, without necessarily rejecting a mismatched one:

| Affinity | Applies To Declarations Containing | Behavior |
|---|---|---|
| **INTEGER** | `INT`, `INTEGER`, `BIGINT` | Prefers whole numbers |
| **TEXT** | `CHAR`, `CLOB`, `TEXT` | Prefers storing as text |
| **REAL** | `REAL`, `FLOAT`, `DOUBLE` | Prefers floating-point numbers |
| **NUMERIC** | `NUMERIC`, `DECIMAL`, `BOOLEAN`, `DATE` | Converts to integer or real when possible |
| **BLOB** | No type specified, or `BLOB` | Stores the value exactly as given |

> 💡 Because of this, column type names in SQLite tutorials — `INT`, `VARCHAR(255)`, `DATETIME` — are really just hints. SQLite doesn't have a dedicated `DATE` or `BOOLEAN` storage class at all; dates are conventionally stored as `TEXT` (ISO-8601 strings), `REAL` (Julian day numbers), or `INTEGER` (Unix timestamps), and booleans as `INTEGER` `0`/`1`.

---

## 2.2 Creating Tables

`CREATE TABLE` looks the same as in any other SQL database, and declared types are still worth writing for documentation, affinity, and tool compatibility even though SQLite won't strictly enforce them by default:

```sql
CREATE TABLE products (
    id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    price NUMERIC NOT NULL,
    in_stock INTEGER DEFAULT 1,
    created_at TEXT DEFAULT CURRENT_TIMESTAMP
);
```

`NOT NULL`, `DEFAULT`, `UNIQUE`, and `CHECK` constraints all work as covered in Database Fundamentals and are enforced regardless of SQLite's flexible typing.

---

## 2.3 ROWID, INTEGER PRIMARY KEY, And WITHOUT ROWID

By default, every SQLite table has a hidden, auto-generated 64-bit primary key called **ROWID**, which uniquely identifies each row even if you never mention it. Declaring a column as `INTEGER PRIMARY KEY` doesn't create a second key alongside it — it makes that column an **alias for the ROWID itself**, giving the same auto-incrementing behavior as `AUTO_INCREMENT` in MySQL, without needing an extra keyword:

```sql
CREATE TABLE orders (
    id INTEGER PRIMARY KEY,   -- this IS the rowid, auto-increments
    total NUMERIC
);

INSERT INTO orders (total) VALUES (49.99);   -- id is assigned automatically
```

A table can opt out of this hidden key entirely with `WITHOUT ROWID`, which stores rows directly in index order by their declared `PRIMARY KEY` instead — a small optimization for tables with a non-integer key (like a text UUID) that are read far more often than written.

---

## 2.4 STRICT Tables

Modern SQLite (3.37+) offers an opt-in way to get closer to traditional static typing: adding `STRICT` to a table definition makes SQLite actually reject values that don't match a column's declared type, instead of silently accepting them:

```sql
CREATE TABLE inventory (
    id INTEGER PRIMARY KEY,
    quantity INTEGER NOT NULL
) STRICT;

INSERT INTO inventory (quantity) VALUES ('a lot');   -- rejected: not an integer
```

`STRICT` tables only allow a small fixed set of type names (`INTEGER`, `REAL`, `TEXT`, `BLOB`, `ANY`), and are a good default for new schemas where catching type mistakes early matters more than SQLite's traditional flexibility.

[Previous](./[1]-Getting-Started-With-SQLite.md) | [Table of Contents](./[0]-Introduction-to-SQLite.md) | [Next](./[3]-CRUD-And-Querying-In-SQLite.md)
