[Previous](./[21]-Stored-Procedures-and-Functions.md) | [Table of Contents](./%5B0%5D-Introduction-to-SQL.md) | [Next](./[23]-Permissions-and-Security.md)

# Lesson 22 - Triggers

---

## 22.1 What a trigger is

A **trigger** is a block of SQL that runs automatically when a specific event happens on a table — an `INSERT`, `UPDATE`, or `DELETE`. Unlike a stored procedure (Lesson 21), you never call a trigger directly; the database fires it for you.

---

## 22.2 When triggers fire

A trigger's timing and event combine:

| Timing | Events |
|---|---|
| `BEFORE` | `INSERT`, `UPDATE`, `DELETE` |
| `AFTER` | `INSERT`, `UPDATE`, `DELETE` |
| `INSTEAD OF` | `INSERT`, `UPDATE`, `DELETE` (mainly for views) |

- `BEFORE` triggers run before the change is applied — commonly used to validate or modify the incoming data
- `AFTER` triggers run once the change is already applied — commonly used to log the change, update a related table, or send a notification
- `INSTEAD OF` triggers replace the action entirely — most often used to make an otherwise non-updatable view (Lesson 16) support `INSERT`/`UPDATE`/`DELETE`

---

## 22.3 A trigger example (PostgreSQL)

PostgreSQL splits trigger logic into two pieces: a **trigger function** and the **trigger** itself that attaches it to a table and event.

```sql
CREATE TABLE book_price_log (
    log_id SERIAL PRIMARY KEY,
    book_id INTEGER,
    old_price DECIMAL,
    new_price DECIMAL,
    changed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE FUNCTION log_price_change() RETURNS TRIGGER AS $$
BEGIN
    IF OLD.price IS DISTINCT FROM NEW.price THEN
        INSERT INTO book_price_log (book_id, old_price, new_price)
        VALUES (OLD.book_id, OLD.price, NEW.price);
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_log_price_change
AFTER UPDATE ON books
FOR EACH ROW
EXECUTE FUNCTION log_price_change();
```

Now, every time a book's price changes, a row is automatically inserted into `book_price_log` — with no application code needing to remember to do it.

---

## 22.4 OLD and NEW

Inside a trigger, `OLD` refers to the row's values *before* the change, and `NEW` refers to the values *after*:
- `INSERT` triggers: only `NEW` is available (there was no "before" row)
- `DELETE` triggers: only `OLD` is available (there's no "after" row)
- `UPDATE` triggers: both `OLD` and `NEW` are available

---

## 22.5 A trigger example (SQLite)

SQLite's syntax is more compact — no separate function required:
```sql
CREATE TRIGGER trg_log_price_change
AFTER UPDATE ON books
FOR EACH ROW
WHEN OLD.price IS NOT NEW.price
BEGIN
    INSERT INTO book_price_log (book_id, old_price, new_price)
    VALUES (OLD.book_id, OLD.price, NEW.price);
END;
```

---

## 22.6 A trigger example (MySQL)

```sql
DELIMITER //

CREATE TRIGGER trg_log_price_change
AFTER UPDATE ON books
FOR EACH ROW
BEGIN
    IF OLD.price != NEW.price THEN
        INSERT INTO book_price_log (book_id, old_price, new_price)
        VALUES (OLD.book_id, OLD.price, NEW.price);
    END IF;
END //

DELIMITER ;
```

---

## 22.7 Using BEFORE triggers to validate/modify data

```sql
CREATE FUNCTION prevent_negative_price() RETURNS TRIGGER AS $$
BEGIN
    IF NEW.price < 0 THEN
        RAISE EXCEPTION 'Price cannot be negative';
    END IF;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER trg_prevent_negative_price
BEFORE INSERT OR UPDATE ON books
FOR EACH ROW
EXECUTE FUNCTION prevent_negative_price();
```
This rejects any insert/update that would set a negative price — though in practice, a `CHECK` constraint (Lesson 14) is usually the simpler, preferred tool for straightforward validation like this. Triggers earn their complexity for logic that a plain constraint can't express, like writing to a *different* table.

---

## 22.8 Common real-world uses for triggers

- **Audit logging** — automatically recording who changed what, and when
- **Maintaining denormalized/summary data** — e.g., updating a `books_count` column on `authors` whenever a book is added or removed
- **Enforcing complex business rules** that span multiple tables, beyond what a `CHECK` constraint can express
- **Cascading custom logic** beyond what `ON DELETE CASCADE` (Lesson 13) provides

---

## 22.9 Trade-offs of triggers

Triggers run invisibly — anyone looking only at application code won't see them, which can make debugging unexpectedly difficult ("why did this row change when my code never updated it?"). Use them deliberately and document them clearly; overuse of triggers is a common source of hard-to-trace bugs in production systems.

---

## 22.10 Dropping a trigger

```sql
DROP TRIGGER trg_log_price_change ON books;   -- PostgreSQL
DROP TRIGGER trg_log_price_change;            -- SQLite, MySQL
```

---

[Previous](./[21]-Stored-Procedures-and-Functions.md) | [Table of Contents](./%5B0%5D-Introduction-to-SQL.md) | [Next](./[23]-Permissions-and-Security.md)
