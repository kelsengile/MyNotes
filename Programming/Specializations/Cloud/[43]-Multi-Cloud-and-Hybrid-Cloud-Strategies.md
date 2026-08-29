[Previous](./[42]-Resource-Tagging-and-Governance.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[44]-Disaster-Recovery-and-High-Availability.md)

*Multi-Cloud & Advanced Topics*

# Lesson 43 - Multi-Cloud & Hybrid Cloud Strategies

## 43.1 Multi-Cloud

**Multi-cloud**, introduced briefly in Lesson 1, means intentionally using more than one public cloud provider for production workloads. Reasons organizations adopt multi-cloud include: avoiding vendor lock-in, negotiating better pricing by not being fully dependent on one vendor, using best-of-breed services from different providers (e.g. GCP for data/ML, AWS for general compute), meeting regulatory requirements in regions where a specific provider isn't available, and redundancy against a provider-wide outage. The cost is real added complexity — different IAM systems, networking models, and tooling per provider — which is why many organizations choose IaC tools like Terraform (Lesson 21) that offer a single, consistent workflow across providers.

---

## 43.2 Hybrid Cloud

**Hybrid cloud** combines public cloud with private, on-premises infrastructure, connected via dedicated network links (e.g. AWS Direct Connect, Azure ExpressRoute) or VPNs. Common motivations include: gradually migrating from on-premises to cloud over time rather than all at once, keeping specific sensitive data on-premises for regulatory reasons while using the cloud for everything else, bursting to the cloud for extra capacity during demand spikes while keeping a steady baseline on owned hardware, and maintaining specialized on-premises hardware (e.g. legacy systems) that can't easily be replicated in the cloud.

---

## 43.3 Trade-offs

Both strategies trade simplicity for flexibility and risk mitigation:

| | Benefit | Cost |
|---|---|---|
| Multi-cloud | Avoids lock-in, best-of-breed services | Duplicated tooling/expertise, higher operational complexity |
| Hybrid cloud | Gradual migration, regulatory flexibility | Network complexity, connecting two very different environments |
| Single cloud | Simplicity, deep integration, one team to train | Vendor lock-in, single point of provider-wide failure |

Most organizations start on a single cloud provider and only adopt multi-cloud or hybrid strategies once a specific, concrete driver (regulatory requirement, M&A, cost negotiation leverage) justifies the added complexity — it's rarely worth the overhead purely speculatively.

---

[Previous](./[42]-Resource-Tagging-and-Governance.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[44]-Disaster-Recovery-and-High-Availability.md)
