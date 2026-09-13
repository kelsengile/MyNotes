[Previous](./[5]-Transactions-And-MVCC.md) | [Table of Contents](./[0]-Introduction-to-PostgreSQL.md)

*Advanced PostgreSQL*

# Lesson 6 - Roles, Extensions, And The Ecosystem

## 6.1 Roles And Permissions

PostgreSQL uses **roles** for both users and groups — there's no separate concept for each:

```sql
CREATE ROLE app_user WITH LOGIN PASSWORD 'strong_password';

GRANT SELECT, INSERT, UPDATE ON ALL TABLES IN SCHEMA public TO app_user;
```

- `WITH LOGIN` makes the role usable as a login account (otherwise it behaves as a permission group).
- Permissions can be granted at the database, schema, table, or even column level for fine-grained control.

---

## 6.2 Popular Extensions

One of PostgreSQL's defining features is its extension system, which adds new capabilities directly into the database:

- **PostGIS** — turns PostgreSQL into a full geospatial database, storing and querying maps, shapes, and coordinates.
- **pg_stat_statements** — tracks execution statistics for every query, invaluable for performance tuning.
- **pgcrypto** — adds cryptographic functions for hashing and encryption.
- **uuid-ossp** / built-in `gen_random_uuid()` — UUID generation.

```sql
CREATE EXTENSION IF NOT EXISTS postgis;
```

---

## 6.3 Replication And High Availability

PostgreSQL supports **streaming replication**, where a primary server continuously ships its write-ahead log (WAL) to one or more standby replicas, which can serve read queries or take over if the primary fails. Tools like **Patroni** and **repmgr** automate failover for production high-availability setups.

---

## 6.4 When To Choose PostgreSQL

PostgreSQL is a strong choice when:

- You need advanced data types (JSONB, arrays, geospatial data) alongside strict relational integrity.
- Your application relies on complex queries — window functions, CTEs, and full-text search.
- You want an extensible database that can grow into new use cases without switching tools.

Consider **MySQL** instead for simpler, extremely widely-hosted setups, or a NoSQL option like **MongoDB** if your data doesn't fit a relational shape at all.

[Previous](./[5]-Transactions-And-MVCC.md) | [Table of Contents](./[0]-Introduction-to-PostgreSQL.md)