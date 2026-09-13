[Previous](./[1]-Getting-Started-With-MySQL.md) | [Table of Contents](./[0]-Introduction-to-MySQL.md) | [Next](./[3]-CRUD-And-Querying-In-MySQL.md)

*Getting Started*

# Lesson 2 - Data Types And Table Creation In MySQL

## 2.1 MySQL Data Types

MySQL supports the standard SQL data type families, with its own specific names and limits:

| Category | Common Types | Notes |
|---|---|---|
| Integers | `TINYINT`, `SMALLINT`, `INT`, `BIGINT` | Fixed storage size, optional `UNSIGNED` |
| Decimals | `DECIMAL(p,s)`, `FLOAT`, `DOUBLE` | Use `DECIMAL` for money — it's exact |
| Text | `CHAR(n)`, `VARCHAR(n)`, `TEXT` | `VARCHAR` is variable-length, `CHAR` is fixed |
| Date/Time | `DATE`, `DATETIME`, `TIMESTAMP`, `TIME` | `TIMESTAMP` auto-converts to UTC internally |
| Boolean | `BOOLEAN` (alias for `TINYINT(1)`) | MySQL has no true native boolean type |
| Other | `ENUM`, `JSON`, `BLOB` | `ENUM` restricts a column to a fixed list of values |

---

## 2.2 Creating Tables

```sql
CREATE TABLE products (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    price DECIMAL(10,2) NOT NULL,
    in_stock BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

This mirrors the general `CREATE TABLE` syntax from Database Fundamentals, but uses MySQL-specific types (`DECIMAL(10,2)`) and the `AUTO_INCREMENT` keyword.

---

## 2.3 AUTO_INCREMENT And Defaults

MySQL uses `AUTO_INCREMENT` to automatically generate sequential numeric primary keys — you never manually assign an `id` when inserting a row that has it.

- Only one `AUTO_INCREMENT` column is allowed per table, and it must be indexed (usually as the primary key).
- `DEFAULT` values (like `DEFAULT TRUE` or `DEFAULT CURRENT_TIMESTAMP`) fill in a column automatically when no value is provided on insert.

---

## 2.4 Storage Engines: InnoDB vs MyISAM

MySQL's **storage engine** determines how a table actually stores and retrieves its data on disk. The engine is set per-table.

| Engine | Transactions | Foreign Keys | Best For |
|---|---|---|---|
| **InnoDB** (default since MySQL 5.5) | ✅ Yes | ✅ Yes | Almost all modern applications |
| **MyISAM** (legacy) | ❌ No | ❌ No | Rare, read-heavy legacy workloads |

> 💡 Unless you have a specific legacy reason, always use **InnoDB** — it supports transactions, foreign keys, and row-level locking, all of which MyISAM lacks.

[Previous](./[1]-Getting-Started-With-MySQL.md) | [Table of Contents](./[0]-Introduction-to-MySQL.md) | [Next](./[3]-CRUD-And-Querying-In-MySQL.md)