[Previous](./[18]-File-IO-and-the-Filesystem.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[20]-Application-Settings-and-Configuration.md)

*Data & Storage*

# Lesson 19 - Local Databases (SQLite)

## 19.1 Why Desktop Apps Use SQLite

SQLite is a self-contained, serverless database engine that stores an entire database in a single file — no separate server process to install or manage. This makes it the default choice for desktop apps that need structured, queryable local storage (more powerful than flat files, without the operational overhead of a client-server database like PostgreSQL).

---

## 19.2 Basic Schema and Queries

Like any relational database, SQLite organizes data into tables with typed columns, and is queried with SQL:

```sql
CREATE TABLE notes (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  title TEXT NOT NULL,
  body TEXT,
  created_at TEXT DEFAULT CURRENT_TIMESTAMP
);

SELECT * FROM notes WHERE title LIKE '%project%' ORDER BY created_at DESC;
```

---

## 19.3 Accessing SQLite from Code

Every major desktop stack has a mature SQLite library: `Microsoft.Data.Sqlite` or Entity Framework Core for .NET, `better-sqlite3` for Electron/Node, `rusqlite` for Rust/Tauri. Prefer **parameterized queries** over string-concatenated SQL to prevent SQL injection and handle special characters correctly:

```csharp
var cmd = connection.CreateCommand();
cmd.CommandText = "SELECT * FROM notes WHERE title = @title";
cmd.Parameters.AddWithValue("@title", userInput);
```

---

## 19.4 Migrations

As an app evolves, its database schema needs to change without destroying existing users' data. A **migration** is a versioned script that transforms the schema from one version to the next (adding a column, creating an index). Track a schema version number in the database itself and run any pending migrations on startup, so upgrading the app upgrades the data automatically.

[Previous](./[18]-File-IO-and-the-Filesystem.md) | [Table of Contents](./[0]-Introduction-to-Desktop-Development.md) | [Next](./[20]-Application-Settings-and-Configuration.md)
