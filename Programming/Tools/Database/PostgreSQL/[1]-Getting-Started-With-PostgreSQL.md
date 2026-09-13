[Previous](./[0]-Introduction-to-PostgreSQL.md) | [Table of Contents](./[0]-Introduction-to-PostgreSQL.md) | [Next](./[2]-Data-Types-And-Table-Creation-In-PostgreSQL.md)

*Getting Started*

# Lesson 1 - Getting Started With PostgreSQL

## 1.1 What Is PostgreSQL

**PostgreSQL** is an open-source **object-relational database management system** with a 30+ year history of active development. It's widely praised for its strict SQL standards compliance, strong data integrity guarantees, and a rich set of features — advanced data types, full-text search, geospatial support (via extensions), and the ability to define custom functions, types, and operators.

> 💡 **Analogy:** If MySQL is a reliable daily driver, PostgreSQL is more like a database with a toolbox built in — it does everything a relational database should, and gives you the parts to build almost anything else on top.

---

## 1.2 Installing And Connecting

PostgreSQL can be installed natively, via a package manager, or run in Docker. It runs as a background server process listening on port `5432` by default. Connection details include a host, port, username, password, and database name.

```bash
psql -U postgres -h localhost -d postgres
```

---

## 1.3 psql Basics

**psql** is PostgreSQL's official interactive command-line client. A few essential meta-commands (all start with a backslash):

| Command | Purpose |
|---|---|
| `\l` | List all databases |
| `\c dbname` | Connect to a different database |
| `\dt` | List tables in the current database |
| `\d tablename` | Describe a table's columns and constraints |
| `\q` | Quit psql |

---

## 1.4 Databases, Schemas, And Roles

PostgreSQL has an extra layer of organization compared to MySQL:

- A **database** is a fully isolated set of data — you connect to one database at a time.
- A **schema** is a namespace *inside* a database that groups related tables (default schema: `public`). This is different from MySQL, where "schema" is just another word for "database."
- A **role** represents a user or a group of permissions, and can be granted access to specific databases, schemas, or tables.

```sql
CREATE DATABASE shop;
CREATE SCHEMA inventory;
CREATE TABLE inventory.products (...);
```

[Previous](./[0]-Introduction-to-PostgreSQL.md) | [Table of Contents](./[0]-Introduction-to-PostgreSQL.md) | [Next](./[2]-Data-Types-And-Table-Creation-In-PostgreSQL.md)