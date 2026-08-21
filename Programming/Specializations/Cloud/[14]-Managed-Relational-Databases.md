[Previous](./[13]-Storage-Classes-and-Lifecycle-Policies.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[15]-Managed-NoSQL-Databases.md)

*Databases*

# Lesson 14 - Managed Relational Databases

## 14.1 What Is a Managed Database?

A **managed database** service runs a standard database engine (PostgreSQL, MySQL, SQL Server, etc.) on infrastructure the provider operates for you, handling provisioning, patching, backups, and often failover automatically. Instead of installing and administering a database server yourself on a VM, you configure the desired engine/version, instance size, and storage, and the provider handles the operational overhead. This trades some low-level control for dramatically less maintenance work and better out-of-the-box reliability.

---

## 14.2 Common Managed RDBMS Options

- **AWS RDS** — supports PostgreSQL, MySQL, MariaDB, SQL Server, Oracle, and Amazon's own Aurora engine.
- **Azure SQL Database** and **Azure Database for PostgreSQL/MySQL**.
- **Google Cloud SQL** — supports PostgreSQL, MySQL, and SQL Server.

Applications connect to these using the same drivers and SQL syntax as a self-hosted database — the managed service is largely a drop-in replacement, just reached over a network endpoint the provider gives you instead of a server you administer yourself.

---

## 14.3 Backups, Replicas, Failover

Managed relational databases typically offer:

- **Automated backups** — scheduled snapshots retained for a configurable period, plus point-in-time recovery to restore to any moment within that window.
- **Read replicas** — read-only copies of the database that offload read traffic from the primary instance, improving performance for read-heavy applications.
- **Multi-AZ / high-availability deployments** — a synchronously replicated standby in a different Availability Zone that the service automatically fails over to if the primary instance becomes unhealthy, minimizing downtime.

Enabling these features (especially automated backups and Multi-AZ) is considered baseline best practice for any production database, since manual backup scripts and single points of failure are exactly the kind of operational burden managed databases exist to remove.

---

[Previous](./[13]-Storage-Classes-and-Lifecycle-Policies.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[15]-Managed-NoSQL-Databases.md)
