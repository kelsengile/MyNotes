[Previous](./[0]-Introduction-to-MySQL.md) | [Table of Contents](./[0]-Introduction-to-MySQL.md) | [Next](./[2]-Data-Types-And-Table-Creation-In-MySQL.md)

*Getting Started*

# Lesson 1 - Getting Started With MySQL

## 1.1 What Is MySQL

**MySQL** is an open-source **relational database management system (RDBMS)** originally released in 1995 and now owned by Oracle Corporation. It implements the relational model covered in Database Fundamentals — data lives in tables made of rows and columns, and it's queried with standard SQL. MySQL is known for being fast, reliable, and easy to set up, which is why it became the "M" in the classic **LAMP stack** (Linux, Apache, MySQL, PHP) and remains a default choice for web applications today.

> 💡 **Analogy:** If SQL is the language, MySQL is one of several "dialects" that speaks it — mutually understandable with other relational databases, but with its own accent, slang, and shortcuts.

---

## 1.2 Installing And Connecting

MySQL can be installed directly on Windows, macOS, or Linux, or run inside a Docker container. After installation, the database runs as a background **server process** (`mysqld`) that listens for connections, typically on port `3306`.

To connect, you need:

- A **host** (e.g. `localhost` or a remote server address)
- A **port** (default `3306`)
- A **username** and **password**
- Optionally, a specific **database name** to connect to

```bash
mysql -u root -p -h localhost
```

This drops you into an interactive shell where you can type SQL statements directly.

---

## 1.3 The MySQL Client And Workbench

There are two common ways to interact with MySQL:

- **`mysql` CLI** — the command-line client that ships with every MySQL installation. Fast, scriptable, and available everywhere.
- **MySQL Workbench** — an official graphical tool for designing schemas, running queries, and visually managing servers, popular with beginners and DBAs alike.

Many hosting providers and IDEs also offer their own MySQL clients (e.g. TablePlus, DBeaver, phpMyAdmin), but they all ultimately send the same SQL statements to the server.

---

## 1.4 Basic Server Concepts

A single MySQL server can host **multiple databases** (sometimes called "schemas" in MySQL's terminology — the two words are used interchangeably here, unlike in PostgreSQL). Useful starting commands:

```sql
SHOW DATABASES;
CREATE DATABASE shop;
USE shop;
SHOW TABLES;
```

- `SHOW DATABASES;` lists every database on the server.
- `CREATE DATABASE` makes a new one.
- `USE` switches your active database for the current session.
- `SHOW TABLES;` lists tables inside the active database.

[Previous](./[0]-Introduction-to-MySQL.md) | [Table of Contents](./[0]-Introduction-to-MySQL.md) | [Next](./[2]-Data-Types-And-Table-Creation-In-MySQL.md)