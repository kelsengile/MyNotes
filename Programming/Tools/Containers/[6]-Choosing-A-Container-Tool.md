[Previous](./[5]-Container-Runtimes-And-Orchestration.md) | [Table of Contents](./[0]-Introduction-to-Containers.md)

*Ecosystem*

# Lesson 6 - Choosing A Container Tool

## 6.1 Matching Tools to Tasks

Everything covered so far — images, layers, running containers, and orchestration — are concepts, not products. In practice, developers use specific tools to put these concepts into action. Which tool makes sense depends on what you're trying to do: build and run a container on your own machine, or coordinate many containers running across a whole fleet of servers.

| Question | Points toward |
|---|---|
| "I want to build and run a container on my laptop" | Docker |
| "I want to run many containers across many servers reliably" | Kubernetes |
| "I need automatic restarts, scaling, and self-healing" | Kubernetes |
| "I'm just learning the basics hands-on" | Docker first |

> 💡 **Analogy:** Docker is like learning to cook in your own kitchen — you build the dish and taste it yourself. Kubernetes is like running the kitchen operations for a chain of restaurants — the same recipes, but now automated staffing, restocking, and quality control across every location.

## 6.2 Docker for Local Development

Docker is typically where developers first encounter containers hands-on. It provides the tools to build images, run containers, and manage them on a single machine, and its tooling has become a de-facto standard that many other tools build on or interoperate with.

Continue to the **[Introduction to Docker](./Docker/[0]-Introduction-to-Docker.md)** to start learning it directly.

## 6.3 Kubernetes for Production Orchestration

Kubernetes is the most widely used orchestration tool, built to manage containers running across many machines at once. It handles scheduling containers onto machines, restarting failed ones, scaling applications up and down, and much more, all based on a desired state that you describe.

Continue to the **[Introduction to Kubernetes](./Kubernetes/[0]-Introduction-to-Kubernetes.md)** to start learning it directly.

## 6.4 Where to Go Next

Docker and Kubernetes solve different problems and are commonly used together: Docker to build and run containers locally, and Kubernetes to orchestrate those same containers in production. Understanding the fundamentals in this Topic first makes it much easier to see *why* each tool works the way it does, rather than memorizing commands without context.

```
   Fundamentals (this Topic)
            │
            ▼
   ┌─────────────────┐        ┌─────────────────────┐
   │      Docker       │  ───▶  │      Kubernetes       │
   │ build & run        │       │ orchestrate those    │
   │ containers locally  │       │ same containers in   │
   │                    │       │ production, at scale  │
   └─────────────────┘        └─────────────────────┘
```

---

[Previous](./[5]-Container-Runtimes-And-Orchestration.md) | [Table of Contents](./[0]-Introduction-to-Containers.md)
