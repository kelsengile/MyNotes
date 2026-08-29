[Previous](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[2]-Choosing-a-Cloud-Provider.md)

*Getting Started*

# Lesson 1 - What is Cloud Computing? Service & Deployment Models

## 1.1 What Is Cloud Computing?

Cloud computing is the on-demand delivery of computing resources — servers, storage, databases, networking, and software — over the internet, with pay-as-you-go pricing. Instead of buying and maintaining physical hardware, you rent capacity from a cloud provider (such as AWS, Azure, or GCP) and pay only for what you use. Key characteristics include on-demand self-service (provision resources without human interaction from the provider), broad network access, resource pooling (multi-tenant infrastructure shared across customers), rapid elasticity (scale up or down quickly), and measured service (usage is metered and billed).

Cloud computing replaced the older model of buying physical servers, racking them in a data center, and manually maintaining them — a model that was slow to scale and expensive to maintain.

---

## 1.2 Service Models: IaaS, PaaS, SaaS

Cloud services are typically grouped into three layers based on how much the provider manages for you:

- **IaaS (Infrastructure as a Service)** — you rent raw compute, storage, and networking (e.g. a virtual machine). You manage the OS, runtime, and application. Examples: AWS EC2, Azure Virtual Machines, GCP Compute Engine.
- **PaaS (Platform as a Service)** — the provider manages the OS and runtime; you just deploy your application code. Examples: AWS Elastic Beanstalk, Azure App Service, Google App Engine.
- **SaaS (Software as a Service)** — a fully finished application delivered over the internet; you just use it. Examples: Gmail, Salesforce, Dropbox.

As you move from IaaS to SaaS, you get less control but less operational responsibility.

---

## 1.3 Deployment Models: Public, Private, Hybrid, Multi-Cloud

- **Public cloud** — infrastructure owned and operated by a third-party provider and shared across many customers (AWS, Azure, GCP).
- **Private cloud** — infrastructure dedicated to a single organization, either on-premises or hosted, offering more control at higher cost.
- **Hybrid cloud** — a mix of public and private infrastructure, connected so workloads can move between them.
- **Multi-cloud** — using more than one public cloud provider simultaneously, often to avoid vendor lock-in or use best-of-breed services.

Most organizations start in the public cloud because it requires no upfront capital investment and scales elastically with demand.

---

[Previous](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[2]-Choosing-a-Cloud-Provider.md)
