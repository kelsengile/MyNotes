[Previous](./[0]-Introduction-to-SQLite.md) | [Table of Contents](./[0]-Introduction-to-SQLite.md) | [Next](./[2]-Data-Types-And-Table-Creation-In-SQLite.md)

*Getting Started*

# Lesson 1 - Getting Started With SQLite

## 1.1 What Is SQLite

**SQLite** is a **relational database engine** distributed as a small, self-contained C library rather than a standalone application. It implements the relational model covered in Database Fundamentals — tables, rows, columns, SQL — but instead of running as a background server that clients connect to over a network, it reads and writes directly to a single file on disk, right from inside the host application's process.

> 💡 **Analogy:** If MySQL and PostgreSQL are restaurants — a separate kitchen (server) that takes orders from many tables (clients) at once — SQLite is a home kitchen built into your own house. There's no waitstaff and no phone line; the "database" is just a cupboard (a file) that your own application reaches into directly.

This design makes SQLite **serverless** and **zero-configuration** — there's no process to install, start, or administer, and no separate step of "connecting" over a network.

---

## 1.2 Installing And Connecting

SQLite ships as a library, so most languages already include it or offer a lightweight package for it (Python's standard library, for example, bundles a `sqlite3` module). The command-line shell is a separate, optional download for exploring databases interactively:

```bash
sqlite3 shop.db
```

Unlike `mysql -u root -p -h localhost`, this command needs no host, port, username, or password — `shop.db` is simply a path to a file. If the file doesn't exist yet, SQLite creates it as soon as data is actually written to it.

---

## 1.3 The sqlite3 CLI And Dot-Commands

Inside the `sqlite3` shell, plain SQL statements work exactly as expected, but the shell also has its own special **dot-commands** (not SQL, and not terminated with a semicolon) for meta-tasks:

```
.tables                 -- list tables in the current database
.schema products        -- show the CREATE TABLE statement for a table
.headers on             -- show column names in query output
.mode column            -- pretty-print results in aligned columns
.open inventory.db       -- switch to (or create) a different database file
.quit                   -- exit the shell
```

> 💡 Dot-commands are handled entirely by the `sqlite3` client itself, not the database engine — a GUI tool or your application's driver won't recognize them, since they aren't SQL.

---

## 1.4 SQLite As An Embedded Database

Because SQLite has no client-server split (as introduced in Database Fundamentals, Lesson 3), the "connection" in application code is really just opening the file:

```python
import sqlite3

conn = sqlite3.connect("shop.db")
cursor = conn.cursor()
cursor.execute("SELECT * FROM products")
```

There's no network round-trip, no authentication handshake, and nothing else to run alongside the application — the entire database lives in that one `shop.db` file, which can be copied, moved, or emailed like any other file. This makes SQLite an excellent fit for mobile apps, desktop software, browsers, and prototypes, but a poor fit for situations where many separate applications on different machines need to share the same data at once (see [Lesson 6](./[6]-Backups-Extensions-And-The-SQLite-Ecosystem.md), 6.4).

[Previous](./[0]-Introduction-to-SQLite.md) | [Table of Contents](./[0]-Introduction-to-SQLite.md) | [Next](./[2]-Data-Types-And-Table-Creation-In-SQLite.md)
