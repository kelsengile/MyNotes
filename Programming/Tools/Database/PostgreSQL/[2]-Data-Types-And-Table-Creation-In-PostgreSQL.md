[Previous](./[1]-Getting-Started-With-PostgreSQL.md) | [Table of Contents](./[0]-Introduction-to-PostgreSQL.md) | [Next](./[3]-Querying-In-PostgreSQL.md)

*Getting Started*

# Lesson 2 - Data Types And Table Creation In PostgreSQL

## 2.1 Standard And Advanced Data Types

PostgreSQL supports the standard relational types, plus several advanced ones that go beyond what most relational databases offer:

| Category | Types |
|---|---|
| Numeric | `INTEGER`, `BIGINT`, `NUMERIC(p,s)`, `REAL`, `DOUBLE PRECISION` |
| Text | `VARCHAR(n)`, `TEXT`, `CHAR(n)` |
| Date/Time | `DATE`, `TIMESTAMP`, `TIMESTAMPTZ`, `INTERVAL` |
| Boolean | `BOOLEAN` (a true native type, unlike MySQL) |
| Advanced | `UUID`, `ARRAY`, `JSON` / `JSONB`, `HSTORE`, `INET` |

---

## 2.2 Arrays And JSONB

PostgreSQL can store a native **array** directly in a column:

```sql
CREATE TABLE posts (
    id SERIAL PRIMARY KEY,
    tags TEXT[]
);

INSERT INTO posts (tags) VALUES (ARRAY['sql', 'databases', 'postgres']);
```

**JSONB** stores JSON data in a parsed, indexable binary format (as opposed to plain `JSON`, which stores it as text). This lets PostgreSQL blend relational structure with flexible, schema-less data in the same table:

```sql
CREATE TABLE events (
    id SERIAL PRIMARY KEY,
    payload JSONB
);

SELECT payload->>'user_id' FROM events WHERE payload->>'type' = 'signup';
```

---

## 2.3 Creating Tables And Constraints

```sql
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    price NUMERIC(10,2) NOT NULL CHECK (price >= 0),
    sku UUID DEFAULT gen_random_uuid() UNIQUE
);
```

PostgreSQL supports the full range of constraints from Database Fundamentals — `NOT NULL`, `UNIQUE`, `CHECK`, `PRIMARY KEY`, and `FOREIGN KEY` — enforced strictly at all times.

---

## 2.4 SERIAL vs IDENTITY

`SERIAL` is PostgreSQL's traditional way to auto-generate integer primary keys (it's shorthand for an integer column backed by a sequence). The modern, SQL-standard alternative is `GENERATED ... AS IDENTITY`:

```sql
-- Traditional
id SERIAL PRIMARY KEY

-- Modern, standards-compliant equivalent
id INTEGER GENERATED ALWAYS AS IDENTITY PRIMARY KEY
```

New projects are generally encouraged to use `IDENTITY`, since it behaves more predictably and matches the SQL standard used by other databases.

[Previous](./[1]-Getting-Started-With-PostgreSQL.md) | [Table of Contents](./[0]-Introduction-to-PostgreSQL.md) | [Next](./[3]-Querying-In-PostgreSQL.md)