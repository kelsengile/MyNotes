[Previous](./[39]-Building-a-Serverless-API.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[41]-Cost-Optimization-Strategies.md)

*Cost & Optimization*

# Lesson 40 - Understanding Cloud Billing & Pricing Models

## 40.1 Pricing Models

Cloud pricing generally follows a few core models:

- **On-demand (pay-as-you-go)** — pay per hour/second of usage with no upfront commitment; most flexible, highest per-unit price.
- **Reserved/committed use** — commit to a certain usage level for 1–3 years in exchange for a significant discount (often 30–70% off on-demand).
- **Spot/preemptible** — bid on unused provider capacity at steep discounts (up to 90% off), with the trade-off that the provider can reclaim the instance with little notice — suitable for fault-tolerant, interruptible workloads.
- **Serverless/consumption-based** — pay only for actual execution (Lesson 9), with no charge at all when idle.

Storage, data transfer (especially data leaving the cloud, called "egress"), and API requests are typically billed separately from compute, and can be significant, easily overlooked costs.

---

## 40.2 Billing Tools

Providers offer tools to understand and analyze spending: AWS Cost Explorer, Azure Cost Management, Google Cloud Billing Reports. These let you break down spend by service, region, and — critically — by **resource tags** (Lesson 42), which is how most organizations attribute cost to specific teams or projects. Detailed billing exports (e.g. AWS Cost and Usage Reports) provide line-item-level data suitable for deeper analysis or feeding into internal cost-tracking dashboards.

---

## 40.3 Free Tiers and Budgets

All three major providers offer a **free tier** — a set of services usable at no cost up to certain limits, ideal for learning and small projects (though it's easy to accidentally exceed limits and incur charges, so monitoring usage matters even on the free tier). To avoid billing surprises:

- Set up **budgets and billing alerts** that notify you when spending crosses a defined threshold.
- Regularly review the billing dashboard, especially early on, to build intuition for what drives cost.
- Remember that some free-tier limits are per-service and time-limited (e.g. "free for 12 months"), while others are always free up to a small permanent limit — read the specific terms for each service you use.

---

[Previous](./[39]-Building-a-Serverless-API.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[41]-Cost-Optimization-Strategies.md)
