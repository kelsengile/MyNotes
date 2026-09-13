[Previous](./[4]-Indexes-And-The-Query-Optimizer.md) | [Table of Contents](./[0]-Introduction-to-MySQL.md) | [Next](./[6]-Backups-Replication-And-The-MySQL-Ecosystem.md)

*Performance And Administration*

# Lesson 5 - Transactions, Users, And Security

## 5.1 Transactions In InnoDB

InnoDB is fully **ACID-compliant** (see Database Fundamentals, Lesson 12), meaning grouped statements either all succeed or all fail together:

```sql
START TRANSACTION;

UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;

COMMIT;
-- or ROLLBACK; to undo both statements
```

By default, MySQL runs in **autocommit** mode, where every single statement is its own transaction — `START TRANSACTION` turns that off until the next `COMMIT` or `ROLLBACK`.

---

## 5.2 Creating Users And Privileges

MySQL manages access through **user accounts**, each tied to a host they're allowed to connect from:

```sql
CREATE USER 'app_user'@'localhost' IDENTIFIED BY 'strong_password';
```

A user with no privileges granted can log in but can't touch any data — privileges must be explicitly assigned.

---

## 5.3 GRANT And REVOKE

```sql
GRANT SELECT, INSERT, UPDATE ON shop.* TO 'app_user'@'localhost';

REVOKE INSERT ON shop.* FROM 'app_user'@'localhost';

FLUSH PRIVILEGES;
```

- `GRANT` gives specific permissions on specific databases/tables.
- `REVOKE` removes them.
- `shop.*` means "every table in the `shop` database" — permissions can be scoped as broadly or narrowly as needed.

---

## 5.4 Securing A MySQL Server

- Never leave the default `root` account accessible remotely with a weak or blank password.
- Follow the **principle of least privilege** — application accounts should only get the permissions they actually need.
- Keep MySQL updated to patch known vulnerabilities.
- Enable SSL/TLS for connections that travel over an untrusted network.
- Regularly audit `SHOW GRANTS FOR 'user'@'host';` to check what access each account actually has.

[Previous](./[4]-Indexes-And-The-Query-Optimizer.md) | [Table of Contents](./[0]-Introduction-to-MySQL.md) | [Next](./[6]-Backups-Replication-And-The-MySQL-Ecosystem.md)