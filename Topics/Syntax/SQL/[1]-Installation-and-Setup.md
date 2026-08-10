[Previous](./[0]-Introduction.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[2]-SQL-Basics.md)

# Lesson 1 - Installing a Database & First-Time Setup

---

## Why start here?

SQL is a language, not a program. To practice it, you need a **database engine** — software that actually stores your data and understands SQL commands. This lesson gets one running on your computer.

---

## Choosing a database for learning

There are many relational database systems. For this course, we recommend **SQLite** to start, because:

- It requires no server, no installation of background services, and no login
- A whole database is just a single file on your computer
- It's built into Python, and has a simple command-line tool
- Everything you learn transfers almost directly to PostgreSQL, MySQL, and SQL Server later (Lesson 25 covers the differences)

Later lessons will note where PostgreSQL, MySQL, or SQL Server behave differently.

---

## Option A: SQLite (recommended for this course)

### Windows
1. Download the "sqlite-tools" zip from the official SQLite website's download page.
2. Extract it to a folder, e.g. `C:\sqlite`.
3. Add that folder to your `PATH` environment variable so you can run `sqlite3` from any terminal.
4. Open Command Prompt and type `sqlite3 --version` to confirm it works.

### macOS
SQLite usually comes preinstalled. Open Terminal and run:
```bash
sqlite3 --version
```
If it's missing, install via Homebrew:
```bash
brew install sqlite
```

### Linux
Most distributions include it, or you can install it:
```bash
sudo apt-get install sqlite3      # Debian/Ubuntu
sudo dnf install sqlite           # Fedora
```

### Creating your first database

```bash
sqlite3 practice.db
```

This opens an interactive SQL prompt and creates a file called `practice.db` in your current folder (the file isn't actually written to disk until you create something in it). You'll see a prompt like:

```
sqlite>
```

Try it out:
```sql
CREATE TABLE greeting (message TEXT);
INSERT INTO greeting VALUES ('Hello, SQL!');
SELECT * FROM greeting;
```

To exit: type `.quit` and press Enter.

---

## Option B: A GUI tool (optional but helpful)

Typing commands is great for learning, but a graphical tool makes it easier to browse tables and see results in a grid. Popular free options:

- **DB Browser for SQLite** — a lightweight GUI specifically for SQLite files
- **DBeaver** — a free, universal database GUI that works with SQLite, PostgreSQL, MySQL, SQL Server, and more
- **TablePlus** (free tier) — polished interface for many databases

Any of these can open the `practice.db` file you just created.

---

## Option C: Practice online, no installation

If you'd rather not install anything yet, sites like SQLite's own "Try SQLite" playground, or sites such as DB Fiddle and SQL Fiddle, let you run SQL directly in the browser. This is a fine way to follow along with the early lessons.

---

## Useful SQLite command-line shortcuts

These start with a dot and are specific to the `sqlite3` tool (not standard SQL):

| Command | What it does |
|---|---|
| `.tables` | List all tables in the current database |
| `.schema tablename` | Show the CREATE TABLE statement for a table |
| `.headers on` | Show column names in query results |
| `.mode column` | Format results in aligned columns |
| `.quit` | Exit the tool |

Turn on friendlier output now — it'll make every later lesson easier to read:
```
.headers on
.mode column
```

---

## Looking ahead

Once you have PostgreSQL, MySQL, or SQL Server installed (later, if you want a "real" server-based database), the SQL commands you learn here will work almost unchanged. Lesson 25 walks through exactly what's different between the major systems.

---

[Previous](./[0]-Introduction.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[2]-SQL-Basics.md)
