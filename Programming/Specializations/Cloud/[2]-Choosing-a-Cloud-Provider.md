[Previous](./[1]-What-is-Cloud-Computing.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[3]-Cloud-Environment-and-CLI-Tools.md)

*Getting Started*

# Lesson 2 - Choosing a Cloud Provider (AWS, Azure, GCP)

## 2.1 The Big Three and Their Ecosystems

**Amazon Web Services (AWS)** launched in 2006 and is the largest provider by market share, with the broadest catalog of services and the largest community. **Microsoft Azure** is popular with enterprises already invested in Microsoft products (Windows Server, Active Directory, .NET) and integrates tightly with them. **Google Cloud Platform (GCP)** is known for strength in data analytics, machine learning, and Kubernetes (which Google originally created). Smaller providers like DigitalOcean, Oracle Cloud, and IBM Cloud serve specific niches such as simplicity or existing enterprise contracts.

---

## 2.2 Comparing Services Across Providers

The three major providers offer largely equivalent core services under different names:

| Category | AWS | Azure | GCP |
|---|---|---|---|
| Virtual Machines | EC2 | Virtual Machines | Compute Engine |
| Object Storage | S3 | Blob Storage | Cloud Storage |
| Managed Kubernetes | EKS | AKS | GKE |
| Serverless Functions | Lambda | Functions | Cloud Functions |
| Relational Database | RDS | Azure SQL | Cloud SQL |

Learning the concepts in this course transfers across providers — the underlying ideas (compute, storage, IAM, networking) are the same everywhere, only the product names and console layouts differ.

---

## 2.3 Factors to Consider When Choosing

When picking a provider for a real project, consider:

- **Existing skills and team experience** — the fastest provider to ship with is often the one your team already knows.
- **Pricing and free tier** — all three offer a free tier for learning and small workloads.
- **Ecosystem fit** — Azure for Microsoft-heavy shops, GCP for data/ML-heavy workloads, AWS for the broadest general-purpose catalog.
- **Compliance and region availability** — check that the provider has data centers in regions required by your regulations or users.
- **Vendor lock-in risk** — using proprietary managed services speeds development but makes switching providers harder later.

For learning purposes, AWS is the most common starting point due to its market share and abundance of tutorials, but the concepts in this course apply to any provider.

---

[Previous](./[1]-What-is-Cloud-Computing.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[3]-Cloud-Environment-and-CLI-Tools.md)
