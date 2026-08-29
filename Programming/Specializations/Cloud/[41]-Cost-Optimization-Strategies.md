[Previous](./[40]-Cloud-Billing-and-Pricing-Models.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[42]-Resource-Tagging-and-Governance.md)

*Cost & Optimization*

# Lesson 41 - Cost Optimization Strategies

## 41.1 Right-Sizing

**Right-sizing** means matching resource size (instance type, database tier, storage class) to actual usage, rather than over-provisioning "just in case." Many workloads run on instances far larger than they need, wasting money on unused capacity. Providers offer recommendations (e.g. AWS Compute Optimizer) based on historical CPU/memory utilization to suggest smaller, cheaper instance types that would still comfortably handle the actual load — right-sizing should be revisited periodically as workload patterns change over time.

---

## 41.2 Reserved/Spot Instances

Building on the pricing models from Lesson 40, real cost savings usually come from matching workload characteristics to the right purchasing option:

- **Reserved/committed use** for steady, predictable baseline workloads that will run continuously for a long period (e.g. a production database that's always on).
- **Spot/preemptible instances** for flexible, interruption-tolerant workloads — batch processing, CI/CD build agents, non-critical background jobs — where losing an instance mid-task is acceptable if the job can retry.
- **On-demand** reserved for genuinely unpredictable or short-term workloads where commitment doesn't make sense.

A common strategy blends all three: a baseline of reserved capacity, spot instances for elastic overflow, and on-demand as a small buffer for unpredictable spikes.

---

## 41.3 Eliminating Waste

Beyond right-sizing and purchasing options, common sources of avoidable cloud waste include:

- **Idle/unused resources** — stopped-but-still-billed volumes, unattached storage, forgotten test environments left running.
- **Over-provisioned auto scaling minimums** (Lesson 10) — keeping more baseline capacity running than actually needed at the lowest-traffic times.
- **Missing lifecycle policies** (Lesson 13) — old data sitting in expensive storage tiers indefinitely.
- **Unoptimized data transfer** — unnecessary cross-region or egress traffic, which is often priced higher than in-region transfer.
- **Orphaned resources** from testing or abandoned projects that were never cleaned up.

Regular cost reviews, combined with good tagging (Lesson 42) to identify resource ownership, are the most reliable way to catch this kind of waste before it accumulates.

---

[Previous](./[40]-Cloud-Billing-and-Pricing-Models.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[42]-Resource-Tagging-and-Governance.md)
