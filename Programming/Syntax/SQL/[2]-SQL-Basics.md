[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./%5B0%5D-Introduction-to-SQL.md) | [Next](./[3]-Filtering-and-Sorting.md)


# Lesson 2 - SQL Basics — SELECT, FROM, WHERE


---

## 2.1 Setting up sample data

Every lesson from here on uses the same small sample database: a fictional bookstore. Run this once in your `sqlite3` prompt (or GUI tool) before following along:

```sql
CREATE TABLE authors (
    author_id INTEGER PRIMARY KEY,
    name TEXT,
    country TEXT
);

CREATE TABLE books (
    book_id INTEGER PRIMARY KEY,
    title TEXT,
    author_id INTEGER,
    price REAL,
    published_year INTEGER,
    genre TEXT
);

INSERT INTO authors VALUES
    (1, 'Ursula K. Le Guin', 'USA'),
    (2, 'Haruki Murakami', 'Japan'),
    (3, 'Chimamanda Ngozi Adichie', 'Nigeria'),
    (4, 'Italo Calvino', 'Italy');

INSERT INTO books VALUES
    (1, 'The Left Hand of Darkness', 1, 9.99, 1969, 'Science Fiction'),
    (2, 'Kafka on the Shore', 2, 12.50, 2002, 'Magical Realism'),
    (3, 'Half of a Yellow Sun', 3, 11.00, 2006, 'Historical Fiction'),
    (4, 'Invisible Cities', 4, 8.75, 1972, 'Fantasy'),
    (5, 'Norwegian Wood', 2, 10.25, 1987, 'Literary Fiction'),
    (6, 'The Dispossessed', 1, 9.50, 1974, 'Science Fiction');
```

We'll keep building on this `authors` / `books` schema throughout the course.

---

## 2.2 The anatomy of a query

The most common SQL statement is `SELECT`. At minimum, it needs two things: **what columns you want**, and **which table to get them from**.

```sql
SELECT column1, column2
FROM table_name;
```

Every SQL statement ends with a semicolon `;`. Some tools don't strictly require it for the last statement, but it's good habit to always include one.

---

## 2.3 SELECT: choosing columns

To get every column, use `*`:
```sql
SELECT * FROM books;
```

To get specific columns, name them, separated by commas:
```sql
SELECT title, price FROM books;
```

Order matters — columns come back in the order you list them, not the order they exist in the table:
```sql
SELECT price, title FROM books;
```

### Renaming columns with AS

You can give a column a temporary name (an "alias") in the output:
```sql
SELECT title AS book_title, price AS cost
FROM books;
```
The `AS` keyword is optional in most databases — `SELECT title book_title` works too — but including it makes queries easier to read.

### Computed columns

You can select expressions, not just raw columns:
```sql
SELECT title, price, price * 1.1 AS price_with_tax
FROM books;
```

---

## 2.4 FROM: choosing the table

`FROM` tells the database where the columns come from. A query always needs exactly one `FROM` clause pointing at a table (or, as you'll see in Lesson 11, multiple tables joined together).

---

## 2.5 WHERE: filtering rows

Without `WHERE`, a query returns every row in the table. `WHERE` narrows that down to rows matching a condition.

```sql
SELECT title, price
FROM books
WHERE price < 10;
```

Common comparison operators:

| Operator | Meaning |
|---|---|
| `=` | equal to |
| `!=` or `<>` | not equal to |
| `<` | less than |
| `>` | greater than |
| `<=` | less than or equal to |
| `>=` | greater than or equal to |

Text values go in single quotes; numbers don't:
```sql
SELECT * FROM books WHERE genre = 'Fantasy';
SELECT * FROM books WHERE published_year > 2000;
```

---

## 2.6 Query clause order

SQL statements are written in a fixed order, even though the database doesn't necessarily *execute* them in that order (more on that in later lessons):

```sql
SELECT ...
FROM ...
WHERE ...
```

Getting this order wrong is one of the most common beginner errors — `WHERE` always comes after `FROM`, never before `SELECT`.

---

## 2.7 Comments

You can annotate your SQL with comments, which the database ignores:
```sql
-- this is a single-line comment
SELECT * FROM books; /* this is a
                         multi-line comment */
```

---

## 2.8 A note on case sensitivity

SQL keywords (`SELECT`, `FROM`, `WHERE`) are traditionally written in uppercase by convention, but SQL doesn't require it — `select * from books;` works identically. Table and column names, however, may or may not be case-sensitive depending on the database system (SQLite is flexible; PostgreSQL lowercases unquoted identifiers). Sticking to lowercase table/column names avoids most headaches.

---

[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./%5B0%5D-Introduction-to-SQL.md) | [Next](./[3]-Filtering-and-Sorting.md)
