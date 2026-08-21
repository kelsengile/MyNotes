[Previous](./[23]-Docker-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[25]-Kubernetes-Fundamentals.md)

*Containers & Orchestration*

# Lesson 24 - Container Registries

## 24.1 What Is a Container Registry?

A **container registry** is a storage and distribution service for container images — similar in concept to how a code repository (like GitHub) stores source code, but for built images. After building an image, you **push** it to a registry; other machines (production servers, orchestrators) then **pull** it from there to run it. This decouples "where an image is built" from "where it runs."

```bash
docker tag my-app:1.0 myregistry.example.com/my-app:1.0
docker push myregistry.example.com/my-app:1.0
```

---

## 24.2 Public vs Private Registries

- **Public registries** — Docker Hub is the default public registry, hosting official images (like `node`, `postgres`, `nginx`) that most Dockerfiles build on top of, as well as public community images.
- **Private/cloud registries** — for proprietary application images, providers offer managed private registries: AWS Elastic Container Registry (ECR), Azure Container Registry (ACR), Google Artifact Registry. These integrate with IAM for access control and typically sit in the same region/network as the compute that will pull from them, minimizing latency and avoiding public internet exposure of your application code.

---

## 24.3 Image Tagging and Versioning

Every image push includes a **tag**, identifying a specific version (e.g. `my-app:1.0`, `my-app:2024-06-01`, or the common but risky `my-app:latest`). Good tagging practices include:

- Avoiding reliance on `:latest` in production — it's mutable and ambiguous about exactly which build is running.
- Using immutable, meaningful tags such as a semantic version (`1.4.2`) or a Git commit SHA, so any deployed image can be traced back to the exact source code it was built from.
- Scanning images for known vulnerabilities before pushing to a registry, a feature most managed registries support automatically.
- Setting cleanup/lifecycle policies on the registry itself to remove old, unused image versions and control storage cost.

---

[Previous](./[23]-Docker-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[25]-Kubernetes-Fundamentals.md)
