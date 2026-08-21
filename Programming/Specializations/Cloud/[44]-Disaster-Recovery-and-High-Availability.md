[Previous](./[43]-Multi-Cloud-and-Hybrid-Cloud-Strategies.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[45]-Cloud-Migration-Strategies.md)

*Multi-Cloud & Advanced Topics*

# Lesson 44 - Disaster Recovery & High Availability

## 44.1 High Availability

**High availability (HA)** means designing a system to remain operational despite the failure of individual components, minimizing downtime. The foundational HA technique, introduced in Lesson 4, is spreading resources across multiple Availability Zones within a region, so a single data center failure doesn't take down the whole application. Combined with load balancing and auto scaling (Lesson 10), HA architectures automatically route traffic away from failed components and replace them, ideally without any human intervention or noticeable user impact.

---

## 44.2 Disaster Recovery Strategies

**Disaster recovery (DR)** goes a step further than HA, planning for larger-scale failures — an entire region becoming unavailable, catastrophic data loss, or a major security incident. Common DR strategies, in increasing order of cost and decreasing order of recovery time:

- **Backup and restore** — regular backups stored in another region; recovery means provisioning fresh infrastructure and restoring from backup, which is slow but cheap.
- **Pilot light** — a minimal version of the environment (e.g. just the database, replicated) is always running in a second region, ready to be scaled up quickly if needed.
- **Warm standby** — a scaled-down but fully functional copy of the environment runs continuously in a second region, ready to take full traffic after being scaled up.
- **Multi-site active-active** — a full, identically-scaled environment runs simultaneously in multiple regions, serving live traffic from all of them at once; fastest recovery, highest cost.

---

## 44.3 RTO and RPO

Two metrics define DR requirements precisely:

- **RTO (Recovery Time Objective)** — the maximum acceptable time to restore service after a disaster. A shorter RTO requires a more expensive, more "hot" DR strategy (closer to active-active).
- **RPO (Recovery Point Objective)** — the maximum acceptable amount of data loss, measured in time (e.g. "we can tolerate losing up to 15 minutes of data"). A shorter RPO requires more frequent backups/replication.

Choosing a DR strategy starts with defining acceptable RTO and RPO for the business, then picking the cheapest strategy that meets those targets — over-engineering DR for a workload that could tolerate hours of downtime wastes money, just as under-engineering it for a critical system risks real business harm.

---

[Previous](./[43]-Multi-Cloud-and-Hybrid-Cloud-Strategies.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[45]-Cloud-Migration-Strategies.md)
