[Previous](./[20]-Security-Basics.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[22]-Software-Development-Lifecycle.md)

# Lesson 21 - Deployment & DevOps Basics

## 21.1 Containers Basics (Docker Concepts)

### The Problem Containers Solve

The classic "it works on my machine" problem occurs when an application behaves differently across environments due to differences in OS versions, installed dependencies, or configuration. **Containers** package an application together with everything it needs to run (code, runtime, libraries, system tools, settings) into a single, portable unit that behaves consistently anywhere it runs.

### Containers vs. Virtual Machines

| | Virtual Machine | Container |
|---|---|---|
| What it virtualizes | Entire hardware (via a hypervisor) | The OS-level environment (shares the host kernel) |
| Includes | Full guest OS | Only the app + its dependencies |
| Startup time | Minutes | Seconds (or less) |
| Resource overhead | Heavy (each VM duplicates a full OS) | Light (shares the host OS kernel) |
| Isolation strength | Very strong (separate kernel) | Strong, but weaker than a VM (shared kernel) |

```
Virtual Machines                    Containers
┌─────────┐ ┌─────────┐            ┌─────────┐ ┌─────────┐
│  App A  │ │  App B  │            │  App A  │ │  App B  │
│ Bins/Lib│ │ Bins/Lib│            │ Bins/Lib│ │ Bins/Lib│
│Guest OS │ │Guest OS │            └─────────┘ └─────────┘
├─────────┴─┴─────────┤            │   Container Engine   │
│      Hypervisor      │           ├───────────────────────┤
├───────────────────────┤          │       Host OS         │
│       Host OS         │          ├───────────────────────┤
├───────────────────────┤          │       Hardware         │
│       Hardware         │         └───────────────────────┘
└───────────────────────┘
```

Containers are lighter weight because they don't virtualize an entire OS — they isolate processes using OS-level features (namespaces, cgroups on Linux) while sharing the host's kernel.

### Docker Core Concepts

**Image** — a read-only, immutable template containing an application and everything it needs to run: code, runtime, libraries, and configuration. Built in layers, and cached for efficiency.

**Container** — a running (or stopped) *instance* of an image. Multiple containers can be started from the same image, each isolated from the others.

**Dockerfile** — a text file with step-by-step instructions for building an image.

```dockerfile
# Start from an official base image
FROM python:3.12-slim

# Set the working directory inside the container
WORKDIR /app

# Copy dependency file first (leverages layer caching)
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy the rest of the application code
COPY . .

# Document the port the app listens on
EXPOSE 8000

# Command to run when the container starts
CMD ["python", "app.py"]
```

**Building and running:**
```bash
docker build -t myapp:1.0 .        # build an image from the Dockerfile
docker run -p 8000:8000 myapp:1.0  # run a container, mapping host:container ports
docker ps                           # list running containers
docker stop <container_id>          # stop a running container
docker images                       # list local images
```

### Image Layers and Caching

Each instruction in a Dockerfile creates a new **layer**, cached independently. Docker reuses cached layers for instructions that haven't changed, speeding up rebuilds significantly — which is why dependency installation is typically placed *before* copying application code (dependencies change less often than code, so that layer stays cached longer).

### Registries

A **container registry** (Docker Hub, Amazon ECR, Google Container Registry, GitHub Container Registry) stores and distributes images, similar to how a package registry (npm, PyPI) distributes libraries.

```bash
docker pull nginx:latest              # download an image from a registry
docker push myregistry.com/myapp:1.0  # upload a built image to a registry
```

### Volumes (Persistent Data)

Containers are ephemeral by design — data written inside a container's writable layer is lost when the container is removed. **Volumes** provide persistent storage that survives container restarts/removal, and can be shared between containers.

```bash
docker run -v mydata:/app/data myapp:1.0   # mount a named volume into the container
```

### Docker Compose (Multi-Container Applications)

Most real applications involve multiple services (a web app, a database, a cache). **Docker Compose** defines and runs multi-container setups from a single configuration file.

```yaml
# docker-compose.yml
services:
  web:
    build: .
    ports:
      - "8000:8000"
    depends_on:
      - db
  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: secret
    volumes:
      - dbdata:/var/lib/postgresql/data

volumes:
  dbdata:
```

```bash
docker compose up      # start all defined services
docker compose down     # stop and remove them
```

### Container Orchestration (Beyond a Single Host)

Running containers reliably at scale — across many machines, with automatic restarts, scaling, and load balancing — is the job of an **orchestrator**:

- **Kubernetes (K8s)** — the dominant container orchestration platform; manages deployment, scaling, networking, and self-healing (automatically restarting failed containers) across clusters of machines.
- **Docker Swarm** — Docker's simpler, built-in orchestration option.
- **Managed alternatives** — cloud-provider offerings like AWS ECS/EKS, Google Cloud Run/GKE, and Azure Container Apps abstract away much of the orchestration complexity.

### Why Containers Matter

- **Consistency** — "works on my machine" becomes "works everywhere" since the environment travels with the application.
- **Isolation** — each container has its own filesystem and dependencies, avoiding version conflicts between applications on the same host.
- **Portability** — the same image runs identically on a developer's laptop, a CI pipeline, and production servers.
- **Efficient resource use** — lighter than VMs, allowing many more containers to run on the same hardware.
- **Fits microservices architectures** well — each service can be packaged, deployed, and scaled independently (see Section 17.3).

---

## 21.2 Cloud Basics (Hosting, Servers, Deployment)

### What "The Cloud" Actually Means

Cloud computing means renting computing resources (servers, storage, databases, networking) from a third-party provider over the internet, rather than owning and maintaining physical hardware yourself. Major providers include **AWS (Amazon Web Services)**, **Google Cloud Platform (GCP)**, and **Microsoft Azure**.

### Service Models

| Model | What's Managed by the Provider | What You Manage | Example |
|---|---|---|---|
| **IaaS** (Infrastructure as a Service) | Physical hardware, virtualization | OS, runtime, app, data | AWS EC2, Google Compute Engine |
| **PaaS** (Platform as a Service) | Hardware + OS + runtime | Application code and data | Heroku, Google App Engine, AWS Elastic Beanstalk |
| **SaaS** (Software as a Service) | Everything — you just use the app | Nothing (just your data/usage) | Gmail, Salesforce, Dropbox |
| **FaaS** (Function as a Service / Serverless) | Everything except your function code | Just the function logic | AWS Lambda, Google Cloud Functions |

```
More control, more responsibility           Less control, less responsibility
◄─────────────────────────────────────────────────────────────────────────►
   On-Premises  →  IaaS  →  PaaS  →  FaaS/Serverless  →  SaaS
```

### Core Cloud Building Blocks

- **Compute** — virtual machines/instances (AWS EC2, GCP Compute Engine) that run your application code.
- **Storage** — object storage for files (AWS S3, GCP Cloud Storage), block storage for VM disks, and file storage for shared filesystems.
- **Managed databases** — the provider handles backups, patching, and scaling of a database engine (AWS RDS, Google Cloud SQL) so you don't manage the underlying server yourself.
- **Networking** — virtual private clouds (VPCs) for isolated network environments, load balancers, DNS management, and content delivery networks (CDNs) for caching content close to users geographically.
- **Identity and Access Management (IAM)** — controls which users/services can access which cloud resources, following the principle of least privilege (see Section 20.3).

### Serverless Computing

"Serverless" doesn't mean there are no servers — it means the developer doesn't manage them. Code runs in short-lived, stateless functions that the cloud provider automatically provisions, scales, and tears down as needed.

```python
# Example: an AWS Lambda function
def handler(event, context):
    name = event.get("name", "World")
    return {"statusCode": 200, "body": f"Hello, {name}!"}
```

**Trade-offs:**
- **Pros:** no server management, automatic scaling (including down to zero when idle), pay only for actual usage/execution time.
- **Cons:** "cold starts" (latency when a function hasn't run recently and needs to initialize), execution time limits, potential vendor lock-in, and added complexity for stateful or long-running workloads.

### Deployment Environments

Most teams maintain multiple environments to safely move code toward production:

| Environment | Purpose |
|---|---|
| **Local/Development** | Where a developer writes and tests code on their own machine |
| **Staging/Test** | A production-like environment for final testing before release |
| **Production** | The live environment actual users interact with |

Configuration (database URLs, API keys, feature flags) typically differs per environment and is managed via environment variables or configuration management tools, never hardcoded — see Section 20.4.

### DNS, Domains, and Deployment

Deploying an application publicly generally involves: provisioning compute/hosting, deploying the application to it, pointing a domain's DNS records (see Section 15.1) at the hosting provider, and configuring HTTPS (often automated today via services like Let's Encrypt).

### Scaling in the Cloud

- **Auto-scaling** — automatically adding/removing compute instances based on real-time demand (e.g., CPU usage or request volume), so capacity matches load without manual intervention.
- **Elasticity** — the ability to rapidly scale resources up or down, a defining advantage of cloud computing over fixed physical infrastructure.
- **Managed load balancers** — cloud providers offer load balancing as a service, distributing traffic across instances (see Section 17.3).

### Cost Considerations

Cloud resources are billed based on usage (compute time, storage, data transfer, requests), which offers flexibility but requires active monitoring — unmanaged resources ("forgetting to turn off" unused servers/instances) is a common source of unexpected cost. Providers offer cost-monitoring dashboards and budget alerts to help track spending.

### Infrastructure as Code (IaC)

Rather than manually clicking through a cloud provider's console to set up resources, **Infrastructure as Code** tools let you define infrastructure in version-controlled configuration files, making deployments repeatable, reviewable, and consistent across environments.

```hcl
# Example: Terraform (a popular IaC tool)
resource "aws_instance" "web_server" {
  ami           = "ami-0abcdef1234567890"
  instance_type = "t3.micro"
}
```

Common tools: **Terraform** (multi-cloud), **AWS CloudFormation** (AWS-specific), **Pulumi** (uses general-purpose programming languages instead of a config DSL).

---

## 21.3 Build, Deploy, and CI/CD Basics

### The Build Process

**Building** transforms source code into a runnable artifact — this might mean compiling code (C, Java, Go), bundling and minifying frontend assets (JavaScript/CSS), installing dependencies, or packaging everything into a container image. The output of a build is typically a deployable **artifact** (a compiled binary, a Docker image, a bundled `.zip`).

```bash
# Example build steps for a typical web app
npm install              # install dependencies
npm run build             # bundle/minify/transpile source into a dist/ folder
docker build -t myapp .   # package it into a container image
```

### Deployment

**Deploying** takes a built artifact and makes it live/available in a target environment (staging, production). This can range from simply copying files to a server, to complex, automated multi-step processes involving orchestration systems.

### Continuous Integration (CI)

CI is the practice of automatically building and testing code every time changes are pushed to a shared repository, catching integration issues and bugs as early as possible — ideally within minutes of a change being made, rather than discovering problems much later.

**A typical CI pipeline, triggered on every push/pull request:**
1. Check out the latest code.
2. Install dependencies.
3. Run linters/static analysis (see Section 12.3).
4. Run the automated test suite (unit, integration — see Section 18).
5. Build the application/artifact.
6. Report success/failure back to the developer (e.g., as a pull request status check).

```yaml
# Example: GitHub Actions CI workflow
name: CI
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: "3.12"
      - run: pip install -r requirements.txt
      - run: pytest
      - run: flake8 .
```

**Benefits:** bugs and integration conflicts are caught immediately (close to when they were introduced, when they're cheapest to fix), and the team maintains confidence that the main branch is always in a working state.

### Continuous Delivery vs. Continuous Deployment

These terms are related but distinct:

- **Continuous Delivery** — every change that passes automated tests is automatically prepared for release (built, tested, packaged) and could be deployed at any time, but the actual deployment to production requires a **manual approval/trigger**.
- **Continuous Deployment** — goes one step further: every change that passes automated tests is **automatically deployed to production** with no manual intervention at all.

```
CI              →  Continuous Delivery         →  Continuous Deployment
Build & Test        Build, Test, Package,          Build, Test, Package,
automatically        ready to deploy               AND deploy — fully automatically
                     (manual trigger to prod)       (no manual step)
```

### Common CI/CD Tools

- **GitHub Actions, GitLab CI/CD** — CI/CD built directly into the code hosting platform, configured via YAML files in the repository.
- **Jenkins** — a widely used, highly configurable, self-hosted automation server.
- **CircleCI, Travis CI** — cloud-hosted CI/CD platforms.
- **ArgoCD, Spinnaker** — specialized continuous delivery tools, often used for Kubernetes deployments.

### Deployment Strategies

Different techniques for rolling out a new version while minimizing risk and downtime:

| Strategy | How It Works |
|---|---|
| **Rolling deployment** | Gradually replaces old instances with new ones, a few at a time, until fully migrated |
| **Blue-green deployment** | Two identical environments ("blue" = current, "green" = new); traffic is switched all at once from blue to green, enabling instant rollback by switching back |
| **Canary deployment** | The new version is rolled out to a small subset of users/traffic first, monitored for issues, then gradually expanded to everyone |
| **Feature flags** | New code is deployed but hidden behind a toggle, allowing features to be enabled/disabled (or gradually rolled out) independently of the deployment itself |

```
Canary example:
Step 1: 5% of traffic → new version, 95% → old version   (monitor for errors)
Step 2: 25% of traffic → new version                       (still monitoring)
Step 3: 100% of traffic → new version                      (fully rolled out)
```

### Rollbacks

A critical safety mechanism: the ability to quickly revert to a previous known-good version if a deployment introduces a serious problem. Well-designed CI/CD pipelines make rollback fast and low-risk — often as simple as re-deploying the previous artifact/image, or (with blue-green deployments) simply switching traffic back to the still-running old environment.

### Monitoring and Observability After Deployment

Deployment isn't the end of the process — teams typically monitor:
- **Application logs** (see Section 12.4) for errors.
- **Metrics** (response times, error rates, resource usage — CPU/memory) via tools like Prometheus, Grafana, or Datadog.
- **Alerts** — automatically notifying the team when key metrics cross concerning thresholds.
- **Health checks** — automated periodic checks (often hitting a `/health` endpoint) that let load balancers/orchestrators detect and route around unhealthy instances.

### A Simplified End-to-End Flow

```
Developer pushes code
       │
       ▼
CI: Build → Lint → Test  ────► (fails → notify developer, stop)
       │  (passes)
       ▼
Package artifact (e.g., Docker image) → push to registry
       │
       ▼
CD: Deploy to staging → automated/manual verification
       │
       ▼
Deploy to production (rolling / blue-green / canary)
       │
       ▼
Monitor logs, metrics, and alerts — rollback if issues appear
```

### Why CI/CD Matters

- **Faster feedback** — bugs are caught within minutes of being introduced, not weeks later.
- **Reduced deployment risk** — small, frequent, automated deployments are far less risky than large, infrequent, manual ones.
- **Consistency** — automated pipelines eliminate "it worked when I deployed it manually" variability between deployments.
- **Confidence to move fast** — a solid CI/CD pipeline with good test coverage (Section 18) lets teams ship changes frequently without fear of breaking production.

[Previous](./[20]-Security-Basics.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[22]-Software-Development-Lifecycle.md)
