[⬅ Back to README](../../README.md)

# Cloud Development

Welcome! This is a self-paced course for learning Cloud Development, the practice of designing, deploying, and operating applications and infrastructure on cloud platforms like AWS, Azure, and GCP.

---

## What is Cloud Development?

Cloud Development lets you:
- Understand core cloud service and deployment models (IaaS, PaaS, SaaS)
- Provision compute, storage, networking, and databases on demand
- Define infrastructure as code and manage it with version control
- Build and orchestrate containers with Docker and Kubernetes
- Design serverless and event-driven systems
- Automate builds and deployments with CI/CD pipelines
- Monitor, log, and trace applications running in production
- Secure cloud resources with IAM, encryption, and secrets management
- Optimize cost, plan for disaster recovery, and architect for high availability

## Table of Contents

**Getting Started**  
    1. **[What is Cloud Computing? Service & Deployment Models](./[1]-What-is-Cloud-Computing.md)**  
       1.1 What Is Cloud Computing?  
       1.2 Service Models: IaaS, PaaS, SaaS  
       1.3 Deployment Models: Public, Private, Hybrid, Multi-Cloud  
    2. **[Choosing a Cloud Provider (AWS, Azure, GCP)](./[2]-Choosing-a-Cloud-Provider.md)**  
       2.1 The Big Three and Their Ecosystems  
       2.2 Comparing Services Across Providers  
       2.3 Factors to Consider When Choosing  
    3. **[Setting Up Your Cloud Environment & CLI Tools](./[3]-Cloud-Environment-and-CLI-Tools.md)**  
       3.1 Creating a Cloud Account  
       3.2 Installing and Configuring the CLI  
       3.3 Authentication and Credentials  

**Core Concepts**  
    4. **[Regions, Availability Zones & Global Infrastructure](./[4]-Regions-Availability-Zones-and-Global-Infrastructure.md)**  
       4.1 Regions  
       4.2 Availability Zones  
       4.3 Edge Locations and Global Infrastructure  
    5. **[IAM: Users, Roles & Permissions](./[5]-IAM-Users-Roles-and-Permissions.md)**  
       5.1 Users and Groups  
       5.2 Roles and Policies  
       5.3 Principle of Least Privilege  
    6. **[Cloud Networking Basics (VPC, Subnets, Security Groups)](./[6]-Cloud-Networking-Basics.md)**  
       6.1 Virtual Private Clouds  
       6.2 Subnets: Public vs Private  
       6.3 Security Groups and Network ACLs  

**Compute**  
    7. **[Virtual Machines & Instances](./[7]-Virtual-Machines-and-Instances.md)**  
       7.1 What Is a Virtual Machine?  
       7.2 Instance Types and Sizing  
       7.3 Managing VM Lifecycle  
    8. **[Containers & Container Orchestration Basics](./[8]-Containers-and-Orchestration-Basics.md)**  
       8.1 What Are Containers?  
       8.2 Containers vs VMs  
       8.3 Why Orchestration?  
    9. **[Serverless Computing (Functions as a Service)](./[9]-Serverless-Computing.md)**  
       9.1 What Is Serverless?  
       9.2 Function as a Service Basics  
       9.3 Use Cases and Trade-offs  
    10. **[Auto Scaling & Load Balancing](./[10]-Auto-Scaling-and-Load-Balancing.md)**  
        10.1 What Is Auto Scaling?  
        10.2 Load Balancers  
        10.3 Scaling Policies  

**Storage**  
    11. **[Object Storage (S3-Style Buckets)](./[11]-Object-Storage.md)**  
        11.1 What Is Object Storage?  
        11.2 Buckets, Keys, and Objects  
        11.3 Access Control and Use Cases  
    12. **[Block & File Storage](./[12]-Block-and-File-Storage.md)**  
        12.1 Block Storage  
        12.2 File Storage  
        12.3 Choosing the Right Storage Type  
    13. **[Storage Classes & Lifecycle Policies](./[13]-Storage-Classes-and-Lifecycle-Policies.md)**  
        13.1 Storage Classes/Tiers  
        13.2 Lifecycle Policies  
        13.3 Cost/Retrieval Trade-offs  

**Databases**  
    14. **[Managed Relational Databases](./[14]-Managed-Relational-Databases.md)**  
        14.1 What Is a Managed Database?  
        14.2 Common Managed RDBMS Options  
        14.3 Backups, Replicas, Failover  
    15. **[Managed NoSQL Databases](./[15]-Managed-NoSQL-Databases.md)**  
        15.1 NoSQL Data Models  
        15.2 Popular Managed NoSQL Services  
        15.3 When to Choose NoSQL  
    16. **[Caching Services (Redis/Memcached)](./[16]-Caching-Services.md)**  
        16.1 Why Caching?  
        16.2 Redis vs Memcached  
        16.3 Caching Strategies  

**Networking & Content Delivery**  
    17. **[DNS & Domain Management in the Cloud](./[17]-DNS-and-Domain-Management.md)**  
        17.1 How DNS Works  
        17.2 Managed DNS Services  
        17.3 Record Types and Routing Policies  
    18. **[Content Delivery Networks (CDNs)](./[18]-Content-Delivery-Networks.md)**  
        18.1 What Is a CDN?  
        18.2 Edge Caching and Origins  
        18.3 CDN Use Cases  
    19. **[API Gateways](./[19]-API-Gateways.md)**  
        19.1 What Is an API Gateway?  
        19.2 Common Features  
        19.3 API Gateway Patterns  

**Infrastructure as Code**  
    20. **[Introduction to Infrastructure as Code](./[20]-Introduction-to-Infrastructure-as-Code.md)**  
        20.1 What Is IaC?  
        20.2 Declarative vs Imperative  
        20.3 Benefits and Tools Overview  
    21. **[Terraform Basics](./[21]-Terraform-Basics.md)**  
        21.1 Terraform Concepts  
        21.2 Providers, Resources, State  
        21.3 Basic Workflow (init/plan/apply)  
    22. **[Cloud-Native IaC Tools (CloudFormation, ARM/Bicep)](./[22]-Cloud-Native-IaC-Tools.md)**  
        22.1 AWS CloudFormation  
        22.2 Azure ARM Templates and Bicep  
        22.3 GCP Deployment Manager / Native Tools  

**Containers & Orchestration**  
    23. **[Docker Fundamentals](./[23]-Docker-Fundamentals.md)**  
        23.1 Images and Containers  
        23.2 Dockerfile Basics  
        23.3 Docker Compose  
    24. **[Container Registries](./[24]-Container-Registries.md)**  
        24.1 What Is a Container Registry?  
        24.2 Public vs Private Registries  
        24.3 Image Tagging and Versioning  
    25. **[Kubernetes Fundamentals](./[25]-Kubernetes-Fundamentals.md)**  
        25.1 Kubernetes Architecture  
        25.2 Pods, Deployments, Services  
        25.3 kubectl Basics  
    26. **[Managed Kubernetes Services (EKS, AKS, GKE)](./[26]-Managed-Kubernetes-Services.md)**  
        26.1 Why Use Managed Kubernetes?  
        26.2 EKS, AKS, GKE Overview  
        26.3 Choosing a Managed Service  

**CI/CD & DevOps**  
    27. **[CI/CD Pipelines in the Cloud](./[27]-CI-CD-Pipelines-in-the-Cloud.md)**  
        27.1 What Is CI/CD?  
        27.2 Pipeline Stages  
        27.3 Cloud CI/CD Tools  
    28. **[Build & Deployment Automation](./[28]-Build-and-Deployment-Automation.md)**  
        28.1 Automated Builds  
        28.2 Deployment Automation  
        28.3 Artifacts and Versioning  
    29. **[Blue-Green & Canary Deployments](./[29]-Blue-Green-and-Canary-Deployments.md)**  
        29.1 Blue-Green Deployments  
        29.2 Canary Deployments  
        29.3 Rollbacks and Risk Mitigation  

**Monitoring & Observability**  
    30. **[Logging & Metrics](./[30]-Logging-and-Metrics.md)**  
        30.1 Why Logging and Metrics Matter  
        30.2 Centralized Logging  
        30.3 Metrics and Dashboards  
    31. **[Distributed Tracing](./[31]-Distributed-Tracing.md)**  
        31.1 What Is Distributed Tracing?  
        31.2 Traces, Spans, Context Propagation  
        31.3 Tracing Tools  
    32. **[Alerting & Incident Response](./[32]-Alerting-and-Incident-Response.md)**  
        32.1 Alerting Basics  
        32.2 On-call and Incident Response  
        32.3 Postmortems  

**Security**  
    33. **[Cloud Security Fundamentals](./[33]-Cloud-Security-Fundamentals.md)**  
        33.1 Shared Responsibility Model  
        33.2 Common Threats  
        33.3 Security Best Practices  
    34. **[Encryption at Rest & in Transit](./[34]-Encryption-at-Rest-and-in-Transit.md)**  
        34.1 Encryption at Rest  
        34.2 Encryption in Transit  
        34.3 Key Management  
    35. **[Secrets Management](./[35]-Secrets-Management.md)**  
        35.1 What Are Secrets?  
        35.2 Secrets Management Services  
        35.3 Best Practices  
    36. **[Compliance & the Shared Responsibility Model](./[36]-Compliance-and-Shared-Responsibility-Model.md)**  
        36.1 Shared Responsibility Recap  
        36.2 Compliance Frameworks  
        36.3 Auditing and Certifications  

**Serverless & Event-Driven Architecture**  
    37. **[Event-Driven Architecture Basics](./[37]-Event-Driven-Architecture-Basics.md)**  
        37.1 What Is Event-Driven Architecture?  
        37.2 Events, Producers, Consumers  
        37.3 Benefits and Challenges  
    38. **[Message Queues & Pub/Sub](./[38]-Message-Queues-and-Pub-Sub.md)**  
        38.1 Message Queues  
        38.2 Pub/Sub  
        38.3 Common Services  
    39. **[Building a Serverless API](./[39]-Building-a-Serverless-API.md)**  
        39.1 Architecture Overview  
        39.2 API Gateway + Functions + Database  
        39.3 Deployment Considerations  

**Cost & Optimization**  
    40. **[Understanding Cloud Billing & Pricing Models](./[40]-Cloud-Billing-and-Pricing-Models.md)**  
        40.1 Pricing Models  
        40.2 Billing Tools  
        40.3 Free Tiers and Budgets  
    41. **[Cost Optimization Strategies](./[41]-Cost-Optimization-Strategies.md)**  
        41.1 Right-Sizing  
        41.2 Reserved/Spot Instances  
        41.3 Eliminating Waste  
    42. **[Resource Tagging & Governance](./[42]-Resource-Tagging-and-Governance.md)**  
        42.1 Why Tagging Matters  
        42.2 Tagging Strategies  
        42.3 Governance Tools  

**Multi-Cloud & Advanced Topics**  
    43. **[Multi-Cloud & Hybrid Cloud Strategies](./[43]-Multi-Cloud-and-Hybrid-Cloud-Strategies.md)**  
        43.1 Multi-Cloud  
        43.2 Hybrid Cloud  
        43.3 Trade-offs  
    44. **[Disaster Recovery & High Availability](./[44]-Disaster-Recovery-and-High-Availability.md)**  
        44.1 High Availability  
        44.2 Disaster Recovery Strategies  
        44.3 RTO and RPO  
    45. **[Cloud Migration Strategies](./[45]-Cloud-Migration-Strategies.md)**  
        45.1 The 6 Rs of Migration  
        45.2 Migration Planning  
        45.3 Common Pitfalls  

**Best Practices**  
    46. **[Cloud Architecture Best Practices (Well-Architected Framework)](./[46]-Cloud-Architecture-Best-Practices.md)**  
        46.1 The Well-Architected Pillars  
        46.2 Applying the Pillars  
        46.3 Continuous Improvement  
    47. **[Cloud Certifications & Career Paths](./[47]-Cloud-Certifications-and-Career-Paths.md)**
        47.1 Why Get Certified?  
        47.2 Popular Certifications  
        47.3 Career Paths in Cloud  