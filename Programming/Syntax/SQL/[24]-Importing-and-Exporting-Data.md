[Previous](./[23]-Permissions-and-Security.md) | [Table of Contents](./%5B0%5D-Introduction-to-SQL.md) | [Next](./[25]-SQL-Dialects.md)


# Lesson 24 - Importing & Exporting Data

---

Real-world data rarely starts out already inside your database — it usually arrives as a CSV export, a JSON API response, or a file from another system. This lesson covers moving data in and out.

---

## Importing CSV data

### SQLite
```
sqlite3 practice.db
.mode csv
.import books.csv books
```
If `books` doesn't already exist, `.import` will create it automatically based on the CSV's columns (typically treating everything as text — you may want to create the table with proper types first, then import).

### PostgreSQL
```sql
COPY books (title, author_id, price, published_year, genre)
FROM '/path/to/books.csv'
DELIMITER ','
CSV HEADER;
```
`CSV HEADER` tells PostgreSQL to skip the first row (column names). `COPY` runs on the server and requires file access on the server's filesystem; the client-side equivalent `psql` command `\copy` reads the file from your local machine instead.

### MySQL
```sql
LOAD DATA INFILE '/path/to/books.csv'
INTO TABLE books
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n'
IGNORE 1 ROWS;
```
`IGNORE 1 ROWS` skips the header row.

### SQL Server
```sql
BULK INSERT books
FROM 'C:\path\to\books.csv'
WITH (
    FIELDTERMINATOR = ',',
    ROWTERMINATOR = '\n',
    FIRSTROW = 2
);
```

---

## Exporting to CSV

### SQLite
```
.headers on
.mode csv
.output books_export.csv
SELECT * FROM books;
.output stdout
```

### PostgreSQL
```sql
COPY (SELECT * FROM books WHERE genre = 'Fantasy')
TO '/path/to/fantasy_books.csv'
DELIMITER ','
CSV HEADER;
```
Notice you can export the result of an arbitrary query, not just a whole table.

### MySQL
```sql
SELECT * FROM books
INTO OUTFILE '/path/to/books_export.csv'
FIELDS TERMINATED BY ','
ENCLOSED BY '"'
LINES TERMINATED BY '\n';
```

### Using a GUI tool or command-line client generically

Most database GUI tools (DBeaver, TablePlus, pgAdmin) offer an "Export results" button after running a query, which sidesteps needing to remember dialect-specific syntax — often the simplest option day-to-day.

---

## Working with JSON

Modern databases increasingly support JSON natively.

### Querying JSON columns (PostgreSQL)
```sql
CREATE TABLE events (id SERIAL PRIMARY KEY, payload JSONB);
INSERT INTO events (payload) VALUES ('{"type": "purchase", "amount": 42.50}');

SELECT payload->>'type' AS event_type, (payload->>'amount')::DECIMAL AS amount
FROM events;
```
`->` extracts a JSON value (keeping JSON type); `->>` extracts it as text.

### Querying JSON columns (MySQL)
```sql
SELECT JSON_EXTRACT(payload, '$.type') AS event_type FROM events;
-- shorthand:
SELECT payload->>'$.type' AS event_type FROM events;
```

### Querying JSON columns (SQLite)
```sql
SELECT json_extract(payload, '$.type') AS event_type FROM events;
```

### Building JSON from relational data
```sql
-- PostgreSQL
SELECT json_build_object('title', title, 'price', price) FROM books;

-- MySQL
SELECT JSON_OBJECT('title', title, 'price', price) FROM books;
```

---

## Dump and restore (full database backups)

### PostgreSQL
```bash
pg_dump mydb > mydb_backup.sql
psql mydb < mydb_backup.sql
```

### MySQL
```bash
mysqldump mydb > mydb_backup.sql
mysql mydb < mydb_backup.sql
```

### SQLite
Since a SQLite database is just a file, backing it up can be as simple as copying the file — though the `.dump` command produces a portable SQL script:
```
sqlite3 practice.db .dump > backup.sql
sqlite3 new_database.db < backup.sql
```

---

## Practical import checklist

Before importing external data:
1. **Inspect the source first** — open the CSV, check column names, encoding, delimiter, and whether it has a header row
2. **Match or create the target schema** — decide types before import, rather than importing everything as text and fixing later
3. **Watch for encoding issues** — mismatched character encodings (like UTF-8 vs Latin-1) commonly corrupt accented characters
4. **Handle missing values deliberately** — decide how blank CSV cells should map to `NULL` vs an empty string vs a default value
5. **Import into a staging table first**, for large or messy datasets — validate and clean there, then move clean rows into the real table, rather than risking corrupting production data directly

---

[Previous](./[23]-Permissions-and-Security.md) | [Table of Contents](./%5B0%5D-Introduction-to-SQL.md) | [Next](./[25]-SQL-Dialects.md)
