[Previous](./[7]-Normalization.md) | [Table of Contents](./[0]-Introduction-to-Databases.md) | [Next](./[9]-CRUD-Operations.md)

*SQL And Querying*

# Lesson 8 - Introduction To SQL

## 8.1 What Is SQL

**SQL** (Structured Query Language) is the standard language used to define, manipulate, and query data in relational databases. It's declarative — you describe *what* data you want, not the exact steps to fetch it — and the DBMS figures out how to retrieve it efficiently. Almost every relational database (PostgreSQL, MySQL, SQLite, SQL Server, Oracle) supports SQL, with small differences in syntax between them.

> 💡 **Analogy:** Ordering at a restaurant is declarative — you say "I'll have the salmon," not "walk to the kitchen, heat the pan, season the fish..." SQL works the same way: you describe the result you want, and the database (the kitchen) figures out the steps.

---

## 8.2 SQL Sublanguages (DDL, DML, DQL, DCL)

SQL statements are often grouped into four categories based on what they do:

- **DDL (Data Definition Language)** — defines structure: `CREATE TABLE`, `ALTER TABLE`, `DROP TABLE`.
- **DML (Data Manipulation Language)** — changes data: `INSERT`, `UPDATE`, `DELETE`.
- **DQL (Data Query Language)** — reads data: `SELECT`.
- **DCL (Data Control Language)** — manages permissions: `GRANT`, `REVOKE`.

DML and DQL together make up what most beginners think of as "SQL queries," and are the focus of [Lesson 9](./[9]-CRUD-Operations.md) through [Lesson 11](./[11]-Joins-And-Subqueries.md).

| Category | Purpose | Example Commands |
|---|---|---|
| DDL | Define structure | CREATE, ALTER, DROP |
| DML | Change data | INSERT, UPDATE, DELETE |
| DQL | Read data | SELECT |
| DCL | Manage permissions | GRANT, REVOKE |

---

## 8.3 Basic Syntax Rules

A few conventions apply across nearly all SQL:

- Statements end with a semicolon (`;`).
- Keywords (`SELECT`, `FROM`, `WHERE`) are traditionally written in uppercase, though SQL is not case-sensitive on keywords.
- String values are wrapped in single quotes: `'like this'`.
- Comments use `--` for a single line or `/* ... */` for a block.

---

## 8.4 Your First Query

The most common SQL statement is `SELECT`, used to read data. A basic query has this shape:

```sql
SELECT first_name, email
FROM users
WHERE created_at > '2026-01-01';
```

Reading it in plain English: "Get the `first_name` and `email` columns, from the `users` table, for rows where `created_at` is after January 1st, 2026." `SELECT` chooses which columns to return, `FROM` chooses which table to read, and `WHERE` filters which rows qualify — a pattern explored in full in the next few lessons.

```
SELECT first_name, email    ← which columns?
FROM users                  ← which table?
WHERE created_at > '2026-01-01';  ← which rows?
```

[Previous](./[7]-Normalization.md) | [Table of Contents](./[0]-Introduction-to-Databases.md) | [Next](./[9]-CRUD-Operations.md)
