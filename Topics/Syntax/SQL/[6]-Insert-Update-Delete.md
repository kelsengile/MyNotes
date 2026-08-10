# Lesson 6: Inserting, Updating & Deleting Data

---

`INSERT`, `UPDATE`, and `DELETE` are the core **DML** (Data Manipulation Language) statements — they change the data inside your tables, not the table structure itself.

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

## A safety habit: SELECT before you UPDATE or DELETE

Before running an `UPDATE` or `DELETE` with a `WHERE` clause you're not 100% sure about, run the equivalent `SELECT` first to see exactly which rows would be affected:
```sql
-- Check first
SELECT * FROM books WHERE genre = 'Fantasy' AND price < 5;

-- Then, once you've confirmed the rows are correct:
DELETE FROM books WHERE genre = 'Fantasy' AND price < 5;
```
This habit prevents the single most common and painful SQL mistake.

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

## RETURNING (PostgreSQL, SQLite 3.35+)

Some databases let you see the affected rows immediately, without a separate `SELECT`:
```sql
UPDATE books SET price = 9.99 WHERE book_id = 1
RETURNING book_id, title, price;
```
**Dialect note:** `RETURNING` is supported by PostgreSQL and modern SQLite, but not by MySQL or SQL Server.

---

## Exercises

Using the `books` table from earlier lessons:

1. Insert a new book: "The Wind-Up Bird Chronicle", by author_id 2, price 13.50, published 1994, genre 'Magical Realism'.
2. Raise the price of all books published before 1980 by $1.
3. Change the genre of book_id 4 to 'Postmodern Fiction'.
4. Delete any book priced above $13.
5. Before running #4, what query would you run to check which rows it will affect?

### Answers

```sql
-- 1
INSERT INTO books (book_id, title, author_id, price, published_year, genre)
VALUES (7, 'The Wind-Up Bird Chronicle', 2, 13.50, 1994, 'Magical Realism');

-- 2
UPDATE books SET price = price + 1 WHERE published_year < 1980;

-- 3
UPDATE books SET genre = 'Postmodern Fiction' WHERE book_id = 4;

-- 4
DELETE FROM books WHERE price > 13;

-- 5
SELECT * FROM books WHERE price > 13;
```


