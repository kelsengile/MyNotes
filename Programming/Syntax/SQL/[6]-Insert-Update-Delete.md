[Previous](./[5]-Creating-Tables.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[7]-Handling-NULLs.md)

# Lesson 6 - Inserting, Updating & Deleting Data

---

`INSERT`, `UPDATE`, and `DELETE` are the core **DML** (Data Manipulation Language) statements — they change the data inside your tables, not the table structure itself.

---

## INSERT: adding rows

### Inserting one row, all columns
```sql
INSERT INTO authors VALUES (5, 'Jorge Luis Borges', 'Argentina');
```
This requires values in the *exact order* the columns were defined — fragile if the table structure ever changes.

### Inserting one row, named columns (recommended)
```sql
INSERT INTO authors (author_id, name, country)
VALUES (5, 'Jorge Luis Borges', 'Argentina');
```
Naming columns explicitly is safer and clearer, and lets you skip columns that have defaults or allow `NULL`.

### Inserting multiple rows at once
```sql
INSERT INTO authors (author_id, name, country) VALUES
    (6, 'Gabriel García Márquez', 'Colombia'),
    (7, 'Toni Morrison', 'USA'),
    (8, 'Yoko Ogawa', 'Japan');
```

### Letting the database generate the ID

If `author_id` is set up as an auto-incrementing primary key, you can omit it and let the database assign the next value:
```sql
INSERT INTO authors (name, country) VALUES ('Isabel Allende', 'Chile');
```

### Inserting from another query
```sql
INSERT INTO archived_books (title, price)
SELECT title, price FROM books WHERE published_year < 1970;
```
This copies rows straight from a `SELECT` into another table.

---

## UPDATE: changing existing rows

```sql
UPDATE books
SET price = 10.99
WHERE book_id = 1;
```

**The `WHERE` clause is critical.** Without it, `UPDATE` changes *every row in the table*:
```sql
-- DANGER: this sets every book's price to 10.99
UPDATE books SET price = 10.99;
```

### Updating multiple columns
```sql
UPDATE books
SET price = 12.99, genre = 'Sci-Fi'
WHERE book_id = 1;
```

### Updating based on the current value
```sql
UPDATE books
SET price = price * 1.05
WHERE genre = 'Science Fiction';
```
This raises the price of every science fiction book by 5%.

---

## DELETE: removing rows

```sql
DELETE FROM books
WHERE book_id = 6;
```

**Again, `WHERE` matters enormously.** Without it, every row is deleted:
```sql
-- DANGER: deletes every row in books
DELETE FROM books;
```
This still leaves the (now empty) table structure intact — unlike `DROP TABLE`, which removes the table itself.

---

## A safety habit: SELECT before you UPDATE or DELETE

Before running an `UPDATE` or `DELETE` with a `WHERE` clause you're not 100% sure about, run the equivalent `SELECT` first to see exactly which rows would be affected:
```sql
-- Check first
SELECT * FROM books WHERE genre = 'Fantasy' AND price < 5;

-- Then, once you've confirmed the rows are correct:
DELETE FROM books WHERE genre = 'Fantasy' AND price < 5;
```
This habit prevents the single most common and painful SQL mistake.

---

## Transactions: an early preview

Server databases (PostgreSQL, MySQL, SQL Server) let you wrap changes in a transaction so you can undo them if something looks wrong, *before* committing:
```sql
BEGIN;
UPDATE books SET price = price * 1.05;
-- check the result...
ROLLBACK;   -- undo, if it looks wrong
-- or:
COMMIT;     -- make it permanent
```
Transactions are covered in full in Lesson 18 — but it's worth knowing this safety net exists even at this early stage.

---

## RETURNING (PostgreSQL, SQLite 3.35+)

Some databases let you see the affected rows immediately, without a separate `SELECT`:
```sql
UPDATE books SET price = 9.99 WHERE book_id = 1
RETURNING book_id, title, price;
```
**Dialect note:** `RETURNING` is supported by PostgreSQL and modern SQLite, but not by MySQL or SQL Server.

---

## UPSERT: insert, or update if it already exists

Often you want to insert a row, but if a row with that same key already exists, update it instead of failing with a duplicate-key error. This pattern is called an **upsert** (insert + update), and every major database supports it — with different syntax.

### PostgreSQL and SQLite: INSERT ... ON CONFLICT
```sql
INSERT INTO authors (author_id, name, country)
VALUES (1, 'Ursula K. Le Guin', 'United States')
ON CONFLICT (author_id)
DO UPDATE SET country = EXCLUDED.country;
```
`ON CONFLICT (author_id)` names the column (usually a primary key or unique constraint) that would trigger a duplicate error. `EXCLUDED` refers to the row that *would* have been inserted — letting you reference its values in the update.

To simply do nothing on conflict, rather than updating:
```sql
INSERT INTO authors (author_id, name, country)
VALUES (1, 'Ursula K. Le Guin', 'USA')
ON CONFLICT (author_id) DO NOTHING;
```

### MySQL: INSERT ... ON DUPLICATE KEY UPDATE
```sql
INSERT INTO authors (author_id, name, country)
VALUES (1, 'Ursula K. Le Guin', 'United States')
ON DUPLICATE KEY UPDATE country = VALUES(country);
```
Here, `VALUES(country)` refers to the value that was supplied in the `INSERT` (MySQL's equivalent of PostgreSQL's `EXCLUDED`).

### SQL Server: MERGE
SQL Server uses a more general-purpose statement, `MERGE`, that can insert, update, *and* delete in one go by comparing a source and a target:
```sql
MERGE INTO authors AS target
USING (VALUES (1, 'Ursula K. Le Guin', 'United States')) AS source (author_id, name, country)
ON target.author_id = source.author_id
WHEN MATCHED THEN
    UPDATE SET country = source.country
WHEN NOT MATCHED THEN
    INSERT (author_id, name, country) VALUES (source.author_id, source.name, source.country);
```
`MERGE` is more verbose but more powerful — it's also available (with different syntax) in PostgreSQL 15+ and Oracle, for cases needing insert/update/delete logic combined in a single statement.

### Why upsert instead of "check, then insert or update"?

Without upsert, application code often does this instead:
```sql
-- 1. Check if it exists
SELECT * FROM authors WHERE author_id = 1;
-- 2. Based on the result, run either an INSERT or an UPDATE
```
This has a race condition: if two processes run this at the same time, both might see "no row exists" and both attempt an `INSERT`, causing one to fail with a duplicate-key error. A database-level upsert handles this atomically, avoiding the gap between checking and acting.

---

[Previous](./[5]-Creating-Tables.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[7]-Handling-NULLs.md)
