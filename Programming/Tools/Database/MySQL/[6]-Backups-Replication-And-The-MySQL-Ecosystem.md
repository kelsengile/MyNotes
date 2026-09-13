[Previous](./[5]-Transactions-Users-And-Security.md) | [Table of Contents](./[0]-Introduction-to-MySQL.md)

*Performance And Administration*

# Lesson 6 - Backups, Replication, And The MySQL Ecosystem

## 6.1 Backing Up With mysqldump

`mysqldump` is MySQL's built-in tool for exporting a database (or the whole server) into a plain SQL file that can recreate it later:

```bash
mysqldump -u root -p shop > shop_backup.sql

mysql -u root -p shop < shop_backup.sql   # restore
```

For very large databases, physical backup tools like **Percona XtraBackup** are often preferred, since they copy raw data files instead of replaying every statement.

---

## 6.2 Replication Basics

**Replication** copies data from one MySQL server (the **primary**, or "source") to one or more other servers (**replicas**) in near real time. This is used to:

- Spread read traffic across multiple servers.
- Provide a standby copy in case the primary fails.
- Run backups or analytics against a replica without affecting production traffic.

MySQL supports both traditional binary-log-based replication and modern **Group Replication** for multi-primary setups.

---

## 6.3 Tools And Ecosystem

- **MySQL Workbench** — official GUI for design, querying, and administration.
- **phpMyAdmin** — a popular web-based admin tool, common in shared hosting environments.
- **Percona Server / MariaDB** — MySQL-compatible forks with additional performance features.
- **ORMs** (Sequelize, SQLAlchemy, Hibernate, Eloquent) — let application code interact with MySQL without writing raw SQL for every query.

---

## 6.4 When To Choose MySQL

MySQL is a strong default choice when:

- You need a proven, widely-supported relational database with a huge community and hosting ecosystem.
- Your application is read-heavy, like most content-driven websites and CMSs (WordPress, Drupal).
- You want simple replication for scaling reads.

Consider **PostgreSQL** instead if you need advanced SQL features, strict standards compliance, or complex data types like native arrays and JSONB (see the PostgreSQL Topic).

[Previous](./[5]-Transactions-Users-And-Security.md) | [Table of Contents](./[0]-Introduction-to-MySQL.md)