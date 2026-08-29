[Previous](./[22]-Triggers.md) | [Table of Contents](./%5B0%5D-Introduction-to-SQL.md) | [Next](./[24]-Importing-and-Exporting-Data.md)

# Lesson 23 - User Permissions & Security

---

This lesson applies to server-based databases (PostgreSQL, MySQL, SQL Server). **SQLite has no user/permission system** — a SQLite database is just a file, and access control means controlling who can access that file at the operating-system level.

---

## Users and roles

A **user** (sometimes called a "login") is an account that can connect to the database. A **role** is a named collection of permissions that can be granted to users — some databases (like PostgreSQL) unify these concepts, treating users as roles that happen to have login privilege.

```sql
-- PostgreSQL / MySQL
CREATE USER analyst WITH PASSWORD 'a_strong_password';   -- PostgreSQL syntax
CREATE USER 'analyst'@'localhost' IDENTIFIED BY 'a_strong_password';  -- MySQL syntax
```

---

## GRANT: giving permissions

```sql
GRANT SELECT ON books TO analyst;
GRANT SELECT, INSERT, UPDATE ON books TO analyst;
GRANT ALL PRIVILEGES ON books TO analyst;
```

Common privilege types:

| Privilege | Allows |
|---|---|
| `SELECT` | Reading data |
| `INSERT` | Adding new rows |
| `UPDATE` | Modifying existing rows |
| `DELETE` | Removing rows |
| `TRUNCATE` | Emptying a table entirely |
| `REFERENCES` | Creating foreign keys pointing at this table |
| `EXECUTE` | Calling a function or stored procedure |
| `ALL PRIVILEGES` | Everything above |

### Granting at different levels

```sql
GRANT SELECT ON books TO analyst;                    -- one table
GRANT SELECT ON ALL TABLES IN SCHEMA public TO analyst; -- every table in a schema (PostgreSQL)
GRANT SELECT (title, price) ON books TO analyst;     -- specific columns only (some databases)
```

---

## REVOKE: taking permissions away

```sql
REVOKE INSERT ON books FROM analyst;
REVOKE ALL PRIVILEGES ON books FROM analyst;
```

---

## Roles as permission groups

Rather than granting permissions to individual users one by one, it's standard practice to define roles representing job functions, then assign users to those roles:
```sql
CREATE ROLE read_only;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO read_only;

GRANT read_only TO analyst;
GRANT read_only TO new_hire;
```
This way, changing what "read-only access" means updates every user in that role at once, rather than requiring individual updates.

---

## The principle of least privilege

A core security principle: every user or application should have the *minimum* permissions necessary to do its job, and nothing more.

- An application's reporting dashboard should typically only have `SELECT` access, never `DELETE` or `DROP`
- A customer-facing application's database user shouldn't be able to alter the schema (`CREATE`, `ALTER`, `DROP`)
- Administrative accounts with full privileges should be used sparingly, not as the default connection for everyday application traffic

This limits the damage from a compromised application, a buggy script, or an accidental mistake — a `DELETE FROM users;` typo is far less catastrophic if that connection only has `SELECT` rights.

---

## Using views to restrict access (recap of Lesson 16)

Views can expose a limited slice of a table's columns or rows, letting you grant broad `SELECT` access to a view without ever exposing sensitive underlying columns directly:
```sql
CREATE VIEW public_authors AS
SELECT author_id, name FROM authors;  -- omits any hypothetical private fields

GRANT SELECT ON public_authors TO analyst;
```

---

## Row-level security (PostgreSQL, SQL Server)

Some databases can restrict access to specific *rows*, not just tables/columns — e.g., letting each salesperson see only their own customers' data:
```sql
-- PostgreSQL
ALTER TABLE orders ENABLE ROW LEVEL SECURITY;

CREATE POLICY salesperson_own_orders ON orders
    FOR SELECT
    USING (salesperson_id = current_user_id());
```

---

## SQL injection: the most important security concept for SQL

**SQL injection** happens when untrusted input (like a search box on a website) is inserted directly into a SQL string, letting an attacker manipulate the query's actual structure.

```sql
-- DANGEROUS: building a query by concatenating user input directly
"SELECT * FROM users WHERE username = '" + user_input + "'"
```
If `user_input` is `' OR '1'='1`, the resulting query becomes:
```sql
SELECT * FROM users WHERE username = '' OR '1'='1'
```
...which returns *every user*, bypassing the intended filter entirely. More malicious input can delete data, extract entire tables, or worse.

### The fix: parameterized queries / prepared statements

Never build SQL by concatenating raw user input. Instead, use placeholders that the database driver fills in safely, keeping user input strictly as *data*, never as executable SQL structure:
```python
# Python example, using a parameterized query
cursor.execute("SELECT * FROM users WHERE username = %s", (user_input,))
```
Every mainstream database driver, in every language, supports parameterized queries — there's essentially never a good reason to concatenate raw strings into SQL.

---

## Encryption

- **Encryption at rest** protects stored data if the underlying disk/backups are stolen
- **Encryption in transit** (TLS/SSL) protects data traveling between application and database over the network
- **Column-level encryption** protects specific sensitive fields (like a password hash or a national ID number) even from users with broad table access

Passwords specifically should never be stored as plain text — always store a salted hash (e.g., bcrypt), computed by the application, never the raw password itself.

---

[Previous](./[22]-Triggers.md) | [Table of Contents](./%5B0%5D-Introduction-to-SQL.md) | [Next](./[24]-Importing-and-Exporting-Data.md)
