[Previous](./[16]-CAP-Theorem-And-Consistency-Models.md) | [Table of Contents](./[0]-Introduction-to-Databases.md) | [Next](./[18]-Scaling-Databases.md)

*Operations And Next Steps*

# Lesson 17 - Database Administration Basics

## 17.1 Users, Roles, and Permissions

Databases enforce access control by defining **users** and granting them **permissions**, often grouped into **roles**, so that not everyone who can connect to the database can do everything to it.

```sql
CREATE ROLE analyst;
GRANT SELECT ON orders TO analyst;

CREATE USER maria WITH PASSWORD 'securepassword';
GRANT analyst TO maria;
```

Here, `maria` inherits the `analyst` role, which can only read (`SELECT`) from the `orders` table — she can't insert, update, or delete data, and has no access to any other table. Following the **principle of least privilege** — giving each user only the access they actually need — limits the damage a mistake or compromised account can cause.

---

## 17.2 Backups and Restores

A **backup** is a saved copy of the database that can be used to restore data after accidental deletion, corruption, or hardware failure. There are two common approaches:

- **Full backups** — a complete snapshot of the entire database at a point in time. Simple to restore from, but larger and slower to create.
- **Incremental backups** — only the changes since the last backup. Faster and smaller, but restoring requires replaying a full backup plus every incremental backup since.

```bash
pg_dump mydatabase > backup.sql
psql mydatabase < backup.sql
```

A backup strategy is only as good as its tested restore process — a backup that has never been restored successfully isn't a reliable safety net, only an assumption.

---

## 17.3 Migrations and Schema Changes

A **migration** is a version-controlled, scripted change to a database's schema (see [5.1](./[5]-Schemas-And-Data-Types.md)), such as adding a column or creating a new table. Migrations let a team apply schema changes consistently across development, staging, and production environments, and roll them back if something goes wrong.

```sql
-- Migration: 0007_add_phone_to_users.sql
ALTER TABLE users ADD COLUMN phone VARCHAR(20);
```

Most teams use a migration tool (such as Flyway, Alembic, or a framework's built-in migration system) that tracks which migrations have already run, so the same script is never applied twice and every environment's schema stays in sync.

---

## 17.4 Monitoring and Maintenance

Keeping a database healthy over time requires ongoing monitoring and maintenance, not just a one-time setup:

- **Monitoring** — tracking metrics like query latency, connection counts, disk usage, and error rates, often with alerts when something crosses a threshold.
- **Maintenance tasks** — routine work like rebuilding fragmented indexes (see [13.1](./[13]-Indexing-And-Query-Performance.md)), updating query planner statistics, and archiving or deleting old data that's no longer needed.
- **Slow query logs** — a record of queries that took longer than expected, often the first place to look when performance degrades.

Treating database administration as an ongoing responsibility, rather than a "set it and forget it" task, is what keeps a production database reliable as data and traffic grow.

[Previous](./[16]-CAP-Theorem-And-Consistency-Models.md) | [Table of Contents](./[0]-Introduction-to-Databases.md) | [Next](./[18]-Scaling-Databases.md)
