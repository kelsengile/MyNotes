[Previous](./[4]-Data-Types-and-Table-Design.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[6]-Insert-Update-Delete.md)

# Lesson 5 - Creating Tables — CREATE, ALTER, DROP

---

These commands belong to a subset of SQL called **DDL** — Data Definition Language — statements that define or change the *structure* of your database, as opposed to **DML** (Data Manipulation Language, Lesson 6) which works with the data itself.

---

## CREATE TABLE

```sql
CREATE TABLE customers (
    customer_id INTEGER PRIMARY KEY,
    first_name TEXT NOT NULL,
    last_name TEXT NOT NULL,
    email TEXT UNIQUE,
    signup_date DATE DEFAULT CURRENT_DATE
);
```

Reading this line by line:
- `customer_id INTEGER PRIMARY KEY` — a unique identifier for each row (Lesson 13 covers primary keys in depth)
- `first_name TEXT NOT NULL` — required text field
- `email TEXT UNIQUE` — text field, but no two rows may share the same value
- `signup_date DATE DEFAULT CURRENT_DATE` — if no value is given on insert, use today's date

### IF NOT EXISTS

Running `CREATE TABLE` on a table that already exists throws an error. To avoid that when re-running scripts:
```sql
CREATE TABLE IF NOT EXISTS customers (
    customer_id INTEGER PRIMARY KEY,
    first_name TEXT
);
```

---

## ALTER TABLE

`ALTER TABLE` changes the structure of an existing table without losing its data.

### Adding a column
```sql
ALTER TABLE customers ADD COLUMN phone TEXT;
```
New columns are `NULL` for all existing rows unless you specify a `DEFAULT`.

### Renaming a column
```sql
ALTER TABLE customers RENAME COLUMN phone TO phone_number;
```

### Renaming a table
```sql
ALTER TABLE customers RENAME TO clients;
```

### Dropping a column
```sql
ALTER TABLE clients DROP COLUMN phone_number;
```

**Dialect note:** Older versions of SQLite (before 3.35) couldn't drop columns directly — you had to recreate the table. Modern SQLite, PostgreSQL, MySQL, and SQL Server all support `DROP COLUMN` directly. Changing a column's data type after creation also varies significantly by dialect — PostgreSQL uses `ALTER COLUMN ... TYPE`, MySQL uses `MODIFY COLUMN`, and SQLite doesn't support it at all without rebuilding the table.

---

## DROP TABLE

Permanently deletes a table and all its data:
```sql
DROP TABLE clients;
```
This cannot be undone (barring backups or, in some transactional contexts, a rollback — see Lesson 18). To avoid an error if the table might not exist:
```sql
DROP TABLE IF EXISTS clients;
```

---

## TRUNCATE TABLE

Removes all *rows* from a table but keeps the table structure — faster than `DELETE FROM table` for clearing large tables, and typically resets auto-increment counters.
```sql
TRUNCATE TABLE clients;
```
**Dialect note:** SQLite doesn't have `TRUNCATE` — use `DELETE FROM clients;` instead, which achieves the same practical result for a learning context.

---

## A full example: building the bookstore schema properly

Combining what we've learned in Lessons 4 and 5:

```sql
DROP TABLE IF EXISTS books;
DROP TABLE IF EXISTS authors;

CREATE TABLE authors (
    author_id INTEGER PRIMARY KEY,
    name TEXT NOT NULL,
    country TEXT
);

CREATE TABLE books (
    book_id INTEGER PRIMARY KEY,
    title TEXT NOT NULL,
    author_id INTEGER,
    price DECIMAL(6,2) NOT NULL,
    published_year INTEGER,
    genre TEXT,
    FOREIGN KEY (author_id) REFERENCES authors(author_id)
);
```

Note the `FOREIGN KEY` line — it tells the database that `books.author_id` refers to `authors.author_id`. Foreign keys are covered fully in Lesson 13.

---

## Viewing a table's structure

- SQLite: `.schema books`
- PostgreSQL: `\d books` (in `psql`)
- MySQL: `DESCRIBE books;` or `SHOW CREATE TABLE books;`
- SQL Server: `EXEC sp_help books;`

---

[Previous](./[4]-Data-Types-and-Table-Design.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[6]-Insert-Update-Delete.md)
