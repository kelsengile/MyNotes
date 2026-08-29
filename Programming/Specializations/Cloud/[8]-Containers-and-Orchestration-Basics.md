[Previous](./[7]-Virtual-Machines-and-Instances.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[9]-Serverless-Computing.md)

*Compute*

# Lesson 8 - Containers & Container Orchestration Basics

## 8.1 What Are Containers?

A **container** packages an application together with all its dependencies (libraries, runtime, config) into a single, portable unit that runs consistently across environments. Unlike a VM, a container doesn't include a full operating system — it shares the host machine's OS kernel while keeping the application isolated in its own filesystem and process space. This makes containers much lighter and faster to start (seconds or less) than VMs. Docker (Lesson 23) is the most widely used tool for building and running containers.

---

## 8.2 Containers vs VMs

| | Virtual Machine | Container |
|---|---|---|
| Includes OS | Full guest OS | Shares host OS kernel |
| Startup time | Minutes | Seconds or less |
| Size | Gigabytes | Megabytes |
| Isolation | Strong (hardware-level) | Process-level |
| Density | Fewer per host | Many per host |

VMs and containers aren't mutually exclusive — in production, containers are usually run *on top of* VMs, combining the strong isolation of a VM boundary with the density and portability of containers.

---

## 8.3 Why Orchestration?

Running one container is easy; running hundreds of containers across many machines reliably is hard. **Container orchestration** tools automate deploying, scaling, networking, and healing containers across a cluster of machines. An orchestrator handles concerns like: which machine should run which container, restarting containers that crash, distributing traffic across replicas, rolling out updates without downtime, and scaling the number of containers up or down based on load. **Kubernetes** (Lesson 25) is the dominant orchestration platform, though simpler alternatives like Docker Compose (single machine) and AWS ECS exist for smaller-scale needs.

---

[Previous](./[7]-Virtual-Machines-and-Instances.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[9]-Serverless-Computing.md)
