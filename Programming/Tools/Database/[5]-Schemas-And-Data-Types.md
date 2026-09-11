[Previous](./[4]-Tables-Rows-And-Columns.md) | [Table of Contents](./[0]-Introduction-to-Databases.md) | [Next](./[6]-Keys-And-Relationships.md)

*Relational Design*

# Lesson 5 - Schemas And Data Types

## 5.1 What Is a Schema

A **schema** is the blueprint that defines the structure of a database: which tables exist, which columns each table has, what data type and constraints each column enforces, and how tables relate to one another. In a relational database, the schema is typically defined before data is inserted, which is why relational databases are often described as having a **fixed** or **strict** schema.

---

## 5.2 Common Data Types

Every column is assigned a data type that determines what kind of value it can store. Common categories, using names similar to most SQL databases:

- **Numeric** — `INTEGER`, `DECIMAL`, `FLOAT` for whole numbers or numbers with decimals.
- **Text** — `VARCHAR(n)` for variable-length text with a maximum size, `TEXT` for unlimited-length text.
- **Date and time** — `DATE`, `TIME`, `TIMESTAMP` for calendar dates and points in time.
- **Boolean** — `BOOLEAN` for true/false values.
- **Binary** — `BLOB` or `BYTEA` for raw binary data like images.

Choosing the right data type matters for both correctness (you can't accidentally store text in a numeric column) and performance (smaller, more specific types are faster to store and index).

---

## 5.3 Constraints

**Constraints** are rules attached to columns or tables that the DBMS enforces automatically, rejecting any operation that would violate them. Common constraints include:

- `NOT NULL` — the column must always have a value.
- `UNIQUE` — no two rows may share the same value in this column.
- `PRIMARY KEY` — uniquely identifies each row (covered in [Lesson 6](./[6]-Keys-And-Relationships.md)).
- `FOREIGN KEY` — the value must match a row in another table (covered in [Lesson 6](./[6]-Keys-And-Relationships.md)).
- `CHECK` — the value must satisfy a custom condition, like `age >= 0`.

---

## 5.4 NULL Values

`NULL` is a special marker representing the **absence** of a value — it is not the same as zero, an empty string, or false. A column allows `NULL` unless it's explicitly marked `NOT NULL`.

`NULL` behaves differently from ordinary values in comparisons: `NULL = NULL` does not evaluate to true in SQL, because you can't say two unknowns are equal to each other. Instead, SQL provides `IS NULL` and `IS NOT NULL` specifically for checking whether a value is missing.

[Previous](./[4]-Tables-Rows-And-Columns.md) | [Table of Contents](./[0]-Introduction-to-Databases.md) | [Next](./[6]-Keys-And-Relationships.md)
