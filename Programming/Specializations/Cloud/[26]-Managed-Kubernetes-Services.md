[Previous](./[25]-Kubernetes-Fundamentals.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[27]-CI-CD-Pipelines-in-the-Cloud.md)

*Containers & Orchestration*

# Lesson 26 - Managed Kubernetes Services (EKS, AKS, GKE)

## 26.1 Why Use Managed Kubernetes?

Running a Kubernetes cluster's control plane yourself is operationally demanding — it requires managing highly-available control plane nodes, handling upgrades, and securing the API server. **Managed Kubernetes services** take on that burden: the provider runs and maintains the control plane, while you focus on your worker nodes and workloads. This significantly lowers the barrier to adopting Kubernetes in production.

---

## 26.2 EKS, AKS, GKE Overview

- **Amazon EKS (Elastic Kubernetes Service)** — deeply integrated with AWS IAM, VPC networking, and other AWS services; supports both self-managed and fully serverless (Fargate) worker nodes.
- **Azure AKS (Azure Kubernetes Service)** — notable for having no charge for the control plane itself (you only pay for worker nodes), and tight Azure AD integration.
- **Google GKE (Google Kubernetes Engine)** — generally considered the most mature managed Kubernetes offering, since Google originally created Kubernetes; offers both a standard mode and a fully "Autopilot" mode that manages node infrastructure for you as well.

All three let you use standard `kubectl` and Kubernetes YAML manifests — application configuration is largely portable between them.

---

## 26.3 Choosing a Managed Service

The choice of managed Kubernetes service usually follows the choice of cloud provider made in Lesson 2, since each is tied to its own provider's IAM, networking, and billing. Beyond that, considerations include:

- **Node management model** — do you want to manage worker node VMs yourself, or use a serverless node option (Fargate on EKS, Autopilot on GKE) that removes node management entirely at a cost premium?
- **Cost of the control plane** — AKS's free control plane can matter for smaller clusters.
- **Ecosystem integration** — how tightly the service integrates with your provider's IAM, logging, and networking tools.

For learning Kubernetes concepts, all three are functionally equivalent; the underlying Kubernetes API and objects (Lesson 25) work identically regardless of which manages the control plane.

---

[Previous](./[25]-Kubernetes-Fundamentals.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[27]-CI-CD-Pipelines-in-the-Cloud.md)
