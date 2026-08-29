[Previous](./[12]-Subqueries.md) | [Table of Contents](./%5B0%5D-Introduction-to-SQL.md) | [Next](./[14]-Constraints-and-Data-Integrity.md)

# Lesson 13 - Keys & Relationships — Primary and Foreign Keys



---

Keys are how relational databases keep data connected and consistent. This lesson explains what they are conceptually; Lesson 14 covers how to enforce them with more constraint types.

---

## Primary keys

A **primary key** uniquely identifies each row in a table. No two rows can share the same primary key value, and a primary key column cannot be `NULL`.

```sql
CREATE TABLE books (
    book_id INTEGER PRIMARY KEY,
    title TEXT NOT NULL
);
```

### Natural vs surrogate keys

- A **natural key** is a real-world attribute that happens to be unique, like an ISBN for a book, or an email address for a user.
- A **surrogate key** is an artificial identifier created just to serve as a key, usually an auto-incrementing integer or a UUID, with no real-world meaning.

Surrogate keys (like our `book_id`) are generally preferred in practice, because natural keys can change (someone's email address might change) or turn out not to be as unique as assumed (two customers might share a name).

### Auto-incrementing primary keys

Most databases can generate primary key values automatically:
```sql
-- SQLite
CREATE TABLE books (book_id INTEGER PRIMARY KEY AUTOINCREMENT, title TEXT);
-- (In SQLite, plain INTEGER PRIMARY KEY already auto-increments in practice;
--  AUTOINCREMENT adds a stricter guarantee against ID reuse.)

-- PostgreSQL
CREATE TABLE books (book_id SERIAL PRIMARY KEY, title TEXT);
-- or, modern style:
CREATE TABLE books (book_id INTEGER GENERATED ALWAYS AS IDENTITY PRIMARY KEY, title TEXT);

-- MySQL
CREATE TABLE books (book_id INT AUTO_INCREMENT PRIMARY KEY, title TEXT);

-- SQL Server
CREATE TABLE books (book_id INT IDENTITY(1,1) PRIMARY KEY, title TEXT);
```

### Composite primary keys

A primary key can span multiple columns — useful when uniqueness only makes sense as a combination:
```sql
CREATE TABLE enrollments (
    student_id INTEGER,
    course_id INTEGER,
    enrolled_date DATE,
    PRIMARY KEY (student_id, course_id)
);
```
This says a student can't enroll in the same course twice, but can enroll in many different courses, and many students can take the same course.

---

## Foreign keys

A **foreign key** is a column (or set of columns) in one table that references the primary key of another table, establishing a relationship between them.

```sql
CREATE TABLE books (
    book_id INTEGER PRIMARY KEY,
    title TEXT,
    author_id INTEGER,
    FOREIGN KEY (author_id) REFERENCES authors(author_id)
);
```

This tells the database: every value in `books.author_id` must either be `NULL`, or match an existing `author_id` in `authors`. Trying to insert a book with an `author_id` that doesn't exist will be rejected — this is called **referential integrity**.

```sql
-- This fails if author_id 999 doesn't exist in authors:
INSERT INTO books (book_id, title, author_id) VALUES (99, 'Ghost Book', 999);
```

**Important:** SQLite does not enforce foreign keys by default — you must explicitly turn it on per connection:
```sql
PRAGMA foreign_keys = ON;
```
PostgreSQL, MySQL (with InnoDB), and SQL Server enforce foreign keys by default.

---

## Relationship types

### One-to-many

The most common relationship: one author can have many books, but each book has one author. This is modeled exactly as shown above — the "many" side (`books`) holds the foreign key pointing to the "one" side (`authors`).

### Many-to-many

Books can have multiple authors, and authors can write multiple books. This can't be modeled with a single foreign key column — it requires a **junction table** (also called a "join table" or "bridge table"):
```sql
CREATE TABLE book_authors (
    book_id INTEGER,
    author_id INTEGER,
    PRIMARY KEY (book_id, author_id),
    FOREIGN KEY (book_id) REFERENCES books(book_id),
    FOREIGN KEY (author_id) REFERENCES authors(author_id)
);
```
Each row represents one book/author pairing. A book with 2 co-authors gets 2 rows here.

### One-to-one

Less common — each row in table A relates to exactly one row in table B. Usually modeled by putting a `UNIQUE` foreign key in one of the tables, often used to split rarely-used or sensitive columns into a separate table:
```sql
CREATE TABLE user_profiles (
    user_id INTEGER PRIMARY KEY,
    bio TEXT,
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);
```

---

## ON DELETE and ON UPDATE behavior

When a referenced row is deleted or its key changes, you can tell the database what to do to dependent rows:

```sql
CREATE TABLE books (
    book_id INTEGER PRIMARY KEY,
    author_id INTEGER,
    FOREIGN KEY (author_id) REFERENCES authors(author_id)
        ON DELETE CASCADE
        ON UPDATE CASCADE
);
```

| Option | Behavior when the referenced row is deleted |
|---|---|
| `CASCADE` | Automatically delete (or update) dependent rows too |
| `SET NULL` | Set the foreign key column to `NULL` |
| `RESTRICT` / `NO ACTION` | Block the delete if dependent rows exist (the default) |
| `SET DEFAULT` | Set the foreign key column to its default value |

Choosing the right behavior matters: `CASCADE` on `authors → books` would silently delete every book by an author when that author is deleted — often *not* what you want, so `RESTRICT` or `SET NULL` is usually safer for this relationship.

---

## Why keys matter

Keys are the mechanism that makes joins meaningful (Lesson 11), prevents duplicate or orphaned data, and is foundational to normalization (Lesson 15). Without them, a "relational" database is really just a collection of unrelated spreadsheets.

---

[Previous](./[12]-Subqueries.md) | [Table of Contents](./%5B0%5D-Introduction-to-SQL.md) | [Next](./[14]-Constraints-and-Data-Integrity.md)
