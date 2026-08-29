[Previous](./[27]-CI-CD-Pipelines-in-the-Cloud.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[29]-Blue-Green-and-Canary-Deployments.md)

*CI/CD & DevOps*

# Lesson 28 - Build & Deployment Automation

## 28.1 Automated Builds

An **automated build** compiles source code, resolves dependencies, runs linters/static analysis, and produces a deployable artifact — all triggered automatically, without a person manually running these steps on their own machine. Automating the build removes "it works on my machine" inconsistencies, since the build always runs the same way, on the same clean environment, regardless of who wrote the code. Build automation is typically the first stage of a CI/CD pipeline (Lesson 27).

---

## 28.2 Deployment Automation

**Deployment automation** takes a built artifact and gets it running in a target environment without manual steps: copying files to servers, updating a Kubernetes deployment, or triggering a serverless function update. Deployment automation typically includes:

- **Environment promotion** — the same artifact moves through dev → staging → production, so what's tested is exactly what ships.
- **Configuration management** — environment-specific settings (database URLs, feature flags) are injected at deploy time rather than baked into the artifact.
- **Rollback capability** — if a deployment causes problems, automation can revert to the previous known-good version quickly.

---

## 28.3 Artifacts and Versioning

A **build artifact** is the packaged output of a build — a container image, a compiled binary, or a zipped application bundle — that gets stored and deployed as a single, immutable unit. Best practices:

- Build the artifact **once**, then deploy that exact same artifact through every environment, rather than rebuilding for each environment (which risks subtle differences creeping in).
- Store artifacts in a registry (container images in a registry as covered in Lesson 24, or a package repository for other artifact types) with clear version tags.
- Tie every artifact back to the exact source commit it was built from, so any running version can be traced to its code.

This "build once, deploy everywhere" approach is a cornerstone of reliable, reproducible deployment automation.

---

[Previous](./[27]-CI-CD-Pipelines-in-the-Cloud.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[29]-Blue-Green-and-Canary-Deployments.md)
