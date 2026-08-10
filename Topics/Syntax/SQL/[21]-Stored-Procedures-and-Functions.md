# Lesson 21: Stored Procedures & Functions

---

## What they are

**Stored procedures** and **stored functions** are reusable blocks of SQL (often with procedural logic like variables, loops, and conditionals) saved inside the database itself, so they can be called by name instead of rewritten every time.

- A **function** returns a value and can typically be used inside a `SELECT` statement, like a built-in function (`UPPER()`, `AVG()`, etc.).
- A **procedure** performs an action (which may include multiple statements, side effects like inserts/updates) and is invoked with `CALL`, not embedded inside a query.

This is the area of SQL with the **most variation between databases** — there's no single standard procedural language. Each major database has its own dialect:

| Database | Procedural language |
|---|---|
| PostgreSQL | PL/pgSQL (also supports PL/Python, PL/Perl, etc.) |
| MySQL | Its own procedural SQL extension |
| SQL Server | T-SQL |
| SQLite | **No stored procedure/function support at all** |

Because SQLite doesn't support this feature, the examples below use PostgreSQL and MySQL syntax — if you've been following along in SQLite, you'll need a different database (or an online sandbox) to try these directly.

## A simple function (PostgreSQL)

```sql
CREATE FUNCTION price_with_tax(base_price DECIMAL, tax_rate DECIMAL DEFAULT 0.08)
RETURNS DECIMAL AS $$
BEGIN
    RETURN base_price * (1 + tax_rate);
END;
$$ LANGUAGE plpgsql;
```

Using it, just like a built-in function:
```sql
SELECT title, price, price_with_tax(price) AS total_price
FROM books;
```

## A simple function (MySQL)

```sql
DELIMITER //

CREATE FUNCTION price_with_tax(base_price DECIMAL(10,2), tax_rate DECIMAL(4,3))
RETURNS DECIMAL(10,2)
DETERMINISTIC
BEGIN
    RETURN base_price * (1 + tax_rate);
END //

DELIMITER ;
```
(The `DELIMITER` switch is a MySQL-specific quirk — it temporarily changes the statement terminator so the semicolons *inside* the function body don't end the `CREATE FUNCTION` statement early.)

## A stored procedure (PostgreSQL)

Procedures can perform multiple actions and don't need to return a value:
```sql
CREATE PROCEDURE apply_discount(target_genre TEXT, discount_pct DECIMAL)
LANGUAGE plpgsql
AS $$
BEGIN
    UPDATE books
    SET price = price * (1 - discount_pct / 100)
    WHERE genre = target_genre;

    RAISE NOTICE 'Applied % percent discount to genre: %', discount_pct, target_genre;
END;
$$;
```
Calling it:
```sql
CALL apply_discount('Fantasy', 15);
```

## Procedural control flow

Stored procedures/functions typically support:

### Variables
```sql
DECLARE total_books INTEGER;
SELECT COUNT(*) INTO total_books FROM books;
```

### Conditionals
```sql
IF total_books > 100 THEN
    RAISE NOTICE 'Large catalog';
ELSE
    RAISE NOTICE 'Small catalog';
END IF;
```

### Loops
```sql
FOR r IN SELECT * FROM books LOOP
    RAISE NOTICE 'Book: %', r.title;
END LOOP;
```

## Parameters: IN, OUT, INOUT

- `IN` parameters (the default) pass a value into the procedure
- `OUT` parameters let the procedure return a value back to the caller, without using `RETURN`
- `INOUT` parameters do both

```sql
-- PostgreSQL
CREATE PROCEDURE get_book_count(genre_filter TEXT, INOUT result_count INTEGER)
LANGUAGE plpgsql AS $$
BEGIN
    SELECT COUNT(*) INTO result_count FROM books WHERE genre = genre_filter;
END;
$$;
```

## Why use stored procedures/functions?

- **Encapsulation** — complex, multi-step business logic lives in one place, in the database, rather than duplicated across every application that touches the data
- **Performance** — for logic requiring many round trips (loops, conditional branches), running it *inside* the database avoids repeated network round-trips between application and database
- **Security** — you can grant permission to `CALL` a procedure without granting direct access to the underlying tables it touches (ties into Lesson 23)
- **Consistency** — every caller gets the same validated behavior, rather than each application re-implementing (and possibly getting subtly wrong) the same logic

## Trade-offs and criticisms

- Procedural SQL dialects are non-standard and largely non-portable between databases — heavy use locks you into one vendor
- Business logic split between application code and stored procedures can become harder to test, version-control, and reason about compared to keeping it entirely in application code
- Many modern teams deliberately keep the database "thin" (just tables, constraints, and maybe views) and put procedural logic in the application layer instead — this is a genuine, debated trade-off in database design philosophy, not a settled question

## Dropping procedures/functions

```sql
DROP FUNCTION price_with_tax(DECIMAL, DECIMAL);
DROP PROCEDURE apply_discount(TEXT, DECIMAL);
```
(PostgreSQL requires specifying the parameter types, since it allows multiple functions/procedures sharing the same name with different parameter signatures — "overloading.")

---

## Exercises

1. In words, describe the difference between a stored function and a stored procedure.
2. Write a PostgreSQL function `discounted_price(price DECIMAL, pct DECIMAL)` that returns the price after applying a percentage discount.
3. Why is SQLite not a good fit for learning stored procedures?
4. Name one advantage and one disadvantage of putting business logic in stored procedures rather than application code.

### Answers

```sql
-- 1
-- A function returns a single value and can be used inside a SELECT
-- expression, like a built-in function. A procedure performs one or more
-- actions (often with side effects like UPDATEs), is invoked separately
-- with CALL, and doesn't need to return anything.

-- 2
CREATE FUNCTION discounted_price(price DECIMAL, pct DECIMAL)
RETURNS DECIMAL AS $$
BEGIN
    RETURN price * (1 - pct / 100);
END;
$$ LANGUAGE plpgsql;

-- 3
-- SQLite has no support for stored procedures or functions at all — it's
-- designed to be a lightweight, embedded, file-based database without a
-- separate server process to host procedural logic.

-- 4
-- Advantage: logic lives in one place and is enforced consistently for
-- every application/user that touches the database.
-- Disadvantage: procedural SQL is non-portable across database vendors,
-- and can be harder to version-control and test than application code.
```

