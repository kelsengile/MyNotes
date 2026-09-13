[Previous](./[5]-Transactions-Concurrency-And-PRAGMAs.md) | [Table of Contents](./[0]-Introduction-to-SQLite.md)

*SQLite In Production*

# Lesson 6 - Backups, Extensions, And The SQLite Ecosystem

## 6.1 Backing Up A SQLite Database

Because a SQLite database is just one file, the simplest possible backup is a plain file copy — but only when it's safe to guarantee nothing is writing to it at that instant. For a live, in-use database, SQLite provides safer built-in options:

```
.backup main backup.db
```

```sql
VACUUM INTO 'backup.db';
```

- `.backup` (a `sqlite3` CLI dot-command) uses SQLite's **Online Backup API**, safely copying the database even while other connections are actively using it.
- `VACUUM INTO` writes a fresh, defragmented copy of the database to a new file in a single SQL statement, useful for both backups and shrinking a bloated file.

---

## 6.2 Extensions: FTS5 And JSON1

SQLite's core stays deliberately small, but it ships with optional extensions **compiled in** to most standard builds:

- **FTS5** — a full-text search extension. A `products_fts` virtual table can be created alongside a normal table to support fast, ranked keyword search (`MATCH`) instead of slow `LIKE '%...%'` scans.
- **JSON1** — functions like `json_extract()` and `json_each()` for reading and querying JSON stored in a `TEXT` column, similar in spirit to PostgreSQL's native `JSONB` support, but layered on top of ordinary text storage rather than a dedicated type.

```sql
CREATE VIRTUAL TABLE products_fts USING fts5(name, description);

SELECT * FROM products_fts WHERE products_fts MATCH 'wireless keyboard';
```

---

## 6.3 Tools And Ecosystem

- **`sqlite3` CLI** — the official command-line shell, bundled with SQLite itself.
- **DB Browser for SQLite** — a popular free GUI for visually browsing, editing, and querying `.db` files.
- **Language bindings** — nearly every programming language has SQLite support built in or via a lightweight driver (Python, Node.js, Java, Swift, and more), since it's the standard embedded storage engine for mobile (iOS, Android) and desktop apps alike.
- **ORMs** — tools like SQLAlchemy and Prisma support SQLite as a backend, often used for local development even when production runs on PostgreSQL or MySQL.

---

## 6.4 When To Choose SQLite

SQLite is a strong default choice when:

- The application is a mobile app, desktop app, browser extension, or embedded device, and the "database" only needs to be reachable from that one application.
- You want zero setup and zero ongoing administration — no server process to install, patch, or monitor.
- The whole dataset is small enough, or read-heavy enough, that the single-writer model (see [5.2](./[5]-Transactions-Concurrency-And-PRAGMAs.md)) isn't a bottleneck.
- You need a portable, easy-to-share format — a single file that can be copied, version-controlled, or attached to an email.

Consider a client-server database like **MySQL** or **PostgreSQL** instead when many separate applications, on different machines, need to read and write the same data concurrently over a network — a job SQLite's serverless, single-writer design was never meant to do.

[Previous](./[5]-Transactions-Concurrency-And-PRAGMAs.md) | [Table of Contents](./[0]-Introduction-to-SQLite.md)
