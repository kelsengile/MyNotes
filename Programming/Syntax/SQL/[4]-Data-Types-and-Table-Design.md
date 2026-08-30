[Previous](./[3]-Filtering-and-Sorting.md) | [Table of Contents](./%5B0%5D-Introduction-to-SQL.md) | [Next](./[5]-Creating-Tables.md)

# Lesson 4 - Data Types & Table Design

---

Before creating tables in the next lesson, it helps to understand what data types are available and how to think about table design. Data types tell the database what kind of value a column holds, which affects storage, validation, and what operations make sense.

---

## 4.1 Why types matter

If a column is defined to hold numbers, the database prevents you from accidentally storing text like `"twelve"` in it, and it can sort and do math on the values correctly. Types are a form of built-in data validation.

---

## 4.2 Common data type categories

### Numbers

| Category | Examples | Notes |
|---|---|---|
| Integers | `INT`, `INTEGER`, `SMALLINT`, `BIGINT` | Whole numbers; sizes differ in range |
| Decimals | `DECIMAL(p,s)`, `NUMERIC(p,s)` | Exact decimal values — use for money |
| Floating point | `REAL`, `FLOAT`, `DOUBLE PRECISION` | Approximate values — fine for scientific data, risky for money |

`DECIMAL(10,2)` means up to 10 total digits, 2 after the decimal point — perfect for prices like `1234.56`. Floating-point types can introduce tiny rounding errors, which is why financial data typically uses `DECIMAL`/`NUMERIC` instead.

### Text

| Type | Notes |
|---|---|
| `CHAR(n)` | Fixed-length, padded with spaces to length n |
| `VARCHAR(n)` | Variable-length, up to n characters |
| `TEXT` | Variable-length, no practical length limit (in most systems) |

In practice, most modern applications default to `VARCHAR` or `TEXT` and rarely use `CHAR` outside of fixed-format codes like two-letter country codes.

### Dates and times

| Type | Stores |
|---|---|
| `DATE` | Calendar date only, e.g. `2024-03-15` |
| `TIME` | Time of day only, e.g. `14:30:00` |
| `TIMESTAMP` / `DATETIME` | Date and time together |
| `TIMESTAMP WITH TIME ZONE` | Date, time, and time zone offset (PostgreSQL) |

Always store dates using an actual date/time type, not as text — this lets the database sort them correctly and do date arithmetic (Lesson 8).

### Boolean

`BOOLEAN` stores true/false. SQLite doesn't have a true boolean type internally — it stores `0`/`1` as integers, but you can still declare a column `BOOLEAN` for clarity, and most drivers translate it automatically.

### Other useful types

- `BLOB` — raw binary data (images, files)
- `JSON` / `JSONB` — structured JSON documents (PostgreSQL's `JSONB` is indexed and efficient; MySQL and SQLite also support JSON columns)
- `UUID` — universally unique identifiers, often used as primary keys in distributed systems

---

## 4.3 SQLite's flexible typing

Unlike most databases, SQLite uses "type affinity" rather than strict types — you can technically insert text into an integer column. This makes SQLite forgiving for learning, but it's not representative of how PostgreSQL, MySQL, or SQL Server behave, which enforce types strictly. Don't rely on SQLite's flexibility once you move to a "real" server database.

---

## 4.4 Designing a table: thinking in entities

Before writing `CREATE TABLE`, sketch out what real-world "things" (entities) you're modeling and what facts (attributes) you need about each one.

For our bookstore:
- **Author**: name, country
- **Book**: title, price, publish year, genre, and a link to its author

Each entity typically becomes one table. Each attribute typically becomes one column.

---

## 4.5 Choosing appropriate types, column by column

| Column | Good type | Why |
|---|---|---|
| `author_id` | `INTEGER` | Whole number, used as an identifier |
| `name` | `TEXT` / `VARCHAR(100)` | Variable-length text |
| `price` | `DECIMAL(6,2)` | Exact currency values |
| `published_year` | `INTEGER` | Whole number |
| `in_stock` | `BOOLEAN` | True/false flag |
| `created_at` | `TIMESTAMP` | Date and time |

---

## 4.6 One fact, one column

A good rule of thumb: each column should hold one atomic piece of information. Avoid cramming multiple values into a single column, like storing `"Le Guin, Ursula (USA)"` as one text field — that makes filtering and sorting painful. Split it into `first_name`, `last_name`, `country` instead. This idea is developed fully in Lesson 15 (Normalization).

---

## 4.7 Nullable vs required columns

When designing a table, decide for each column: is it okay for this value to be missing (`NULL`), or should every row be required to have one? You'll declare this with `NOT NULL` when creating the table (Lesson 5), and it ties closely into Lesson 14 (Constraints).

---

[Previous](./[3]-Filtering-and-Sorting.md) | [Table of Contents](./%5B0%5D-Introduction-to-SQL.md) | [Next](./[5]-Creating-Tables.md)
