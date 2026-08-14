[Previous](./[13]-Keys-and-Relationships.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[15]-Normalization-and-Schema-Design.md)


# Lesson 14 - Constraints & Data Integrity

---

Constraints are rules attached to columns or tables that the database enforces automatically, rejecting any insert or update that would violate them. They're the difference between "data that's technically stored" and "data you can actually trust."

---

## NOT NULL

Requires a column to always have a value:
```sql
CREATE TABLE customers (
    customer_id INTEGER PRIMARY KEY,
    email TEXT NOT NULL
);
```
```sql
-- Fails: email is required
INSERT INTO customers (customer_id) VALUES (1);
```

---

## UNIQUE

Ensures no two rows share the same value in a column (or set of columns):
```sql
CREATE TABLE customers (
    customer_id INTEGER PRIMARY KEY,
    email TEXT UNIQUE
);
```
Unlike a primary key, a table can have multiple `UNIQUE` constraints, and (in most databases) `UNIQUE` columns can still contain multiple `NULL`s, since `NULL` is never considered equal to another `NULL` (Lesson 7).

### Multi-column UNIQUE
```sql
CREATE TABLE enrollments (
    student_id INTEGER,
    course_id INTEGER,
    UNIQUE (student_id, course_id)
);
```
This allows a student to enroll in many courses, and a course to have many students, but blocks the *same* student from enrolling in the *same* course twice.

---

## CHECK

Enforces an arbitrary condition on a column's value:
```sql
CREATE TABLE books (
    book_id INTEGER PRIMARY KEY,
    price DECIMAL(6,2) CHECK (price > 0),
    published_year INTEGER CHECK (published_year >= 1450)
);
```
```sql
-- Fails: violates the CHECK constraint
INSERT INTO books (book_id, price) VALUES (1, -5.00);
```

`CHECK` can reference multiple columns:
```sql
CREATE TABLE bookings (
    check_in DATE,
    check_out DATE,
    CHECK (check_out > check_in)
);
```

**Dialect note:** MySQL only started *enforcing* `CHECK` constraints in version 8.0.16 (2019) — earlier versions parsed but silently ignored them.

---

## DEFAULT

Not strictly an integrity constraint, but closely related — provides a fallback value when none is given:
```sql
CREATE TABLE orders (
    order_id INTEGER PRIMARY KEY,
    status TEXT DEFAULT 'pending',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

---

## PRIMARY KEY and FOREIGN KEY

Covered in depth in Lesson 13 — both are constraints too. To recap briefly:
```sql
CREATE TABLE books (
    book_id INTEGER PRIMARY KEY,
    author_id INTEGER,
    FOREIGN KEY (author_id) REFERENCES authors(author_id)
);
```

---

## Naming constraints

You can name constraints explicitly, which makes error messages clearer and lets you drop or alter them later by name:
```sql
CREATE TABLE books (
    book_id INTEGER PRIMARY KEY,
    price DECIMAL(6,2),
    CONSTRAINT chk_positive_price CHECK (price > 0),
    CONSTRAINT uq_title UNIQUE (title)
);
```
Without a name, the database auto-generates one (often something unmemorable like `books_price_check`).

---

## Adding constraints to an existing table

```sql
ALTER TABLE books ADD CONSTRAINT chk_price_positive CHECK (price > 0);
ALTER TABLE customers ADD CONSTRAINT uq_email UNIQUE (email);
```
**Dialect note:** SQLite has very limited `ALTER TABLE` support for adding constraints after the fact — in practice, you usually need to create a new table with the constraint, copy the data over, drop the old table, and rename the new one.

---

## Dropping constraints

```sql
-- PostgreSQL / MySQL / SQL Server
ALTER TABLE books DROP CONSTRAINT chk_price_positive;

-- MySQL uses a different syntax specifically for CHECK constraints in some versions:
ALTER TABLE books DROP CHECK chk_price_positive;
```

---

## What happens when a constraint is violated?

The statement is rejected entirely, and (inside a transaction — Lesson 18) any partial changes from that statement are rolled back. The database raises an error identifying which constraint was violated, which you (or your application) can catch and handle.

```
ERROR: duplicate key value violates unique constraint "customers_email_key"
```

---

## Why enforce integrity in the database, not just the application?

It's tempting to think "I'll just validate this in my app code." But database-level constraints matter because:
- Multiple applications, scripts, or people might write to the same database — a constraint enforces the rule *everywhere*, not just in one codebase
- They catch bugs in application logic before bad data gets permanently stored
- They document the actual rules of your data model directly in the schema, where anyone inspecting the database can see them

---

[Previous](./[13]-Keys-and-Relationships.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[15]-Normalization-and-Schema-Design.md)
