[Previous](./[8]-Introduction-To-SQL.md) | [Table of Contents](./[0]-Introduction-to-Databases.md) | [Next](./[10]-Filtering-Sorting-And-Aggregating.md)

*SQL And Querying*

# Lesson 9 - CRUD Operations

## 9.1 Create (INSERT)

`INSERT` adds new rows to a table. You specify the table, which columns you're providing values for, and the values themselves:

```sql
INSERT INTO users (first_name, email)
VALUES ('Dana', 'dana@example.com');
```

Any column left out (like `id`, if it auto-increments) is filled in automatically or defaults to `NULL` if allowed.

---

## 9.2 Read (SELECT)

`SELECT` retrieves rows from a table. Introduced in [Lesson 8](./[8]-Introduction-To-SQL.md), it's the most frequently used SQL statement:

```sql
SELECT * FROM users;
```

The `*` means "all columns." In practice, it's better to list only the columns you actually need — this reduces the amount of data transferred and makes the query's intent clearer.

---

## 9.3 Update (UPDATE)

`UPDATE` modifies existing rows. It requires a `WHERE` clause to specify which rows to change — leaving it out updates **every** row in the table, which is a common and costly mistake:

```sql
UPDATE users
SET email = 'dana.new@example.com'
WHERE id = 4;
```

> ⚠️ **Cautionary Example:** Forgetting the `WHERE` clause on `UPDATE users SET email = 'dana.new@example.com';` doesn't just fix Dana's email — it overwrites *every single user's* email with the same value. This exact mistake has taken down production systems more than once.

---

## 9.4 Delete (DELETE)

`DELETE` removes rows from a table. Like `UPDATE`, it requires a `WHERE` clause to target specific rows:

```sql
DELETE FROM users
WHERE id = 4;
```

Without a `WHERE` clause, `DELETE FROM users;` removes every row in the table. Many teams add a safeguard — like requiring a transaction (see [Lesson 12](./[12]-Transactions-And-ACID.md)) or a confirmation step — before running unqualified `DELETE` or `UPDATE` statements in production.

Together, `INSERT`, `SELECT`, `UPDATE`, and `DELETE` form **CRUD** — Create, Read, Update, Delete — the four basic operations almost every application performs on its data.

> 💡 **Analogy:** CRUD maps almost perfectly onto everyday actions with a physical filing cabinet: **Create** a new folder, **Read** (pull out and look at) a folder, **Update** (edit) its contents, and **Delete** (shred) a folder you no longer need.

| Operation | SQL Statement | Physical Analogy |
|---|---|---|
| Create | INSERT | Add a new folder |
| Read | SELECT | Pull out and read a folder |
| Update | UPDATE | Edit the folder's contents |
| Delete | DELETE | Shred the folder |

[Previous](./[8]-Introduction-To-SQL.md) | [Table of Contents](./[0]-Introduction-to-Databases.md) | [Next](./[10]-Filtering-Sorting-And-Aggregating.md)
