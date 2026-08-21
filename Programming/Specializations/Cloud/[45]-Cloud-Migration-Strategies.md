[Previous](./[44]-Disaster-Recovery-and-High-Availability.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[46]-Cloud-Architecture-Best-Practices.md)

*Multi-Cloud & Advanced Topics*

# Lesson 45 - Cloud Migration Strategies

## 45.1 The 6 Rs of Migration

When moving an existing application from on-premises (or another cloud) into the cloud, teams typically choose from six common strategies, often called the "6 Rs":

- **Rehost ("lift and shift")** — move the application as-is onto cloud VMs, with minimal changes. Fastest, but doesn't take advantage of cloud-native features.
- **Replatform** — make small optimizations during the move (e.g. switch to a managed database) without a full redesign.
- **Repurchase** — replace the application with a SaaS alternative (e.g. move from a self-hosted CRM to Salesforce).
- **Refactor/Re-architect** — redesign the application to be cloud-native (e.g. break a monolith into microservices, adopt serverless), maximizing long-term benefit at the highest short-term effort.
- **Retire** — decommission applications no longer needed, rather than migrating them at all.
- **Retain** — keep certain applications on-premises for now, migrating them later or never (often due to compliance, cost, or complexity).

---

## 45.2 Migration Planning

A successful migration typically follows a structured process: **assess** the current application landscape (dependencies, data volumes, compliance constraints), **plan** which of the 6 Rs applies to each application and in what order, **migrate** in waves — starting with lower-risk, less-critical applications to build confidence and expertise before tackling critical systems, and **optimize** post-migration, since a "lift and shift" migration often needs follow-up work to actually realize cost and performance benefits. Running the on-premises and cloud environments in parallel during a transition period, with careful data synchronization, reduces the risk of a hard cutover.

---

## 45.3 Common Pitfalls

- **Underestimating data transfer time/cost** — moving large datasets can take longer and cost more than expected, especially over standard internet connections; providers offer physical data transfer appliances for very large migrations.
- **Lifting and shifting without any optimization**, resulting in cloud costs that are higher than the original on-premises costs, since the application wasn't adapted to take advantage of elasticity.
- **Underestimating retraining needs** — teams accustomed to on-premises operations need real time to build cloud skills.
- **Skipping security/compliance review** during the rush to migrate, leaving gaps that wouldn't have existed on the tightly-controlled original infrastructure.

---

[Previous](./[44]-Disaster-Recovery-and-High-Availability.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[46]-Cloud-Architecture-Best-Practices.md)
