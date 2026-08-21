[Previous](./[26]-Managed-Kubernetes-Services.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[28]-Build-and-Deployment-Automation.md)

*CI/CD & DevOps*

# Lesson 27 - CI/CD Pipelines in the Cloud

## 27.1 What Is CI/CD?

**CI/CD** stands for **Continuous Integration** and **Continuous Delivery/Deployment**. Continuous Integration means developers frequently merge code changes into a shared branch, with an automated process building and testing every change to catch problems early. Continuous Delivery extends this by automatically preparing every passing change for release; Continuous Deployment goes one step further and automatically releases every passing change to production without manual approval. Together, CI/CD replaces slow, error-prone manual release processes with fast, repeatable, automated ones.

---

## 27.2 Pipeline Stages

A typical CI/CD pipeline runs a sequence of stages whenever code is pushed:

1. **Source** — triggered by a commit/pull request to a repository.
2. **Build** — compile code, install dependencies, build container images.
3. **Test** — run automated unit, integration, and sometimes end-to-end tests.
4. **Deploy to staging** — release to a pre-production environment for further verification.
5. **Deploy to production** — release to end users, often gated by manual approval or automated checks.

If any stage fails, the pipeline stops and the team is notified, preventing broken code from reaching later stages.

---

## 27.3 Cloud CI/CD Tools

Cloud-native and popular third-party CI/CD tools include:

- **AWS CodePipeline/CodeBuild**, **Azure Pipelines**, **Google Cloud Build** — provider-native services, tightly integrated with their respective ecosystems.
- **GitHub Actions**, **GitLab CI/CD** — workflow definitions live alongside the code repository itself, triggered directly by repository events.
- **Jenkins**, **CircleCI** — long-standing, highly configurable options that work across any cloud.

A minimal GitHub Actions workflow:

```yaml
name: CI
on: [push]
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm install
      - run: npm test
```

---

[Previous](./[26]-Managed-Kubernetes-Services.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[28]-Build-and-Deployment-Automation.md)
