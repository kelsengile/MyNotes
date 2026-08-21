[Previous](./[12]-Block-and-File-Storage.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[14]-Managed-Relational-Databases.md)

*Storage*

# Lesson 13 - Storage Classes & Lifecycle Policies

## 13.1 Storage Classes/Tiers

Not all stored data is accessed equally often, so providers offer multiple **storage classes** (also called tiers), trading retrieval speed and availability for cost:

- **Standard/Hot** — for frequently accessed data, highest cost per GB, instant retrieval.
- **Infrequent Access/Cool** — for data accessed occasionally, lower storage cost but a per-retrieval fee.
- **Archive/Cold** (e.g. AWS Glacier) — for rarely accessed data like long-term backups or compliance records, lowest cost, but retrieval can take minutes to hours.

Choosing the right class for each dataset can cut storage costs dramatically without any change to your application logic.

---

## 13.2 Lifecycle Policies

A **lifecycle policy** automates moving objects between storage classes — or deleting them — based on age, without manual intervention. For example, a policy might say: keep objects in Standard for 30 days, move to Infrequent Access at day 30, move to Archive at day 90, and delete at day 365. Example AWS S3 lifecycle rule (simplified):

```json
{
  "Rules": [{
    "Status": "Enabled",
    "Transitions": [
      { "Days": 30, "StorageClass": "STANDARD_IA" },
      { "Days": 90, "StorageClass": "GLACIER" }
    ],
    "Expiration": { "Days": 365 }
  }]
}
```

---

## 13.3 Cost/Retrieval Trade-offs

The core trade-off across storage classes is **storage cost vs retrieval cost/latency**: cheaper classes to store data in are more expensive or slower to read from. Design decisions should be based on actual access patterns — for example, application logs might be read frequently in the first week (Standard), rarely afterward (Infrequent Access), and only needed for audits after a year (Archive). Applying lifecycle policies to logs, backups, and old user data is one of the simplest and most effective cost-optimization techniques (see also Lesson 41).

---

[Previous](./[12]-Block-and-File-Storage.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[14]-Managed-Relational-Databases.md)
