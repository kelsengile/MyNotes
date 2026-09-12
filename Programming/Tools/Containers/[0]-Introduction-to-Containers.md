[⬅ Back to README](../../../README.md)

# Introduction to Containers

Containers are a way of packaging an application together with everything it needs to run — code, runtime, system libraries, and configuration — so that it behaves the same way no matter where it's deployed. This Topic covers the fundamental, tool-agnostic concepts behind containers: what they are, how they differ from virtual machines, how container images work, and how containers are run and orchestrated in practice.

These lessons intentionally avoid tying the concepts to any single tool. Once you understand the fundamentals here, head over to the tool-specific folders below to see how those concepts are put into practice with real, widely-used tools.

## Why Learn Containers?

- **"Works on my machine" stops being an excuse** — a container packages the app with its exact dependencies, so it runs the same way on your laptop, a teammate's laptop, and a production server.
- **It's the default way modern apps ship** — most cloud-native companies deploy software as containers, so the skill shows up in almost every backend, DevOps, or platform role.
- **It's lighter than a full virtual machine** — you get isolation and portability without the overhead of booting an entire guest operating system for every app.
- **It unlocks orchestration** — once you understand a single container, tools like Kubernetes (which run thousands of them) make a lot more sense.

A quick sense of how a container stacks up against the alternative you may already know, a virtual machine:

| Aspect | Virtual Machine | Container |
|---|---|---|
| What it virtualizes | Entire hardware + OS | Just the application layer, sharing the host OS kernel |
| Startup time | Minutes | Seconds |
| Typical size | Gigabytes | Megabytes |
| Isolation level | Very strong (separate kernel) | Process-level (shared kernel) |
| Best for | Running different OSes on one machine | Packaging and shipping apps consistently |

## Container Tools

The lessons above teach the fundamentals that apply across the whole container ecosystem. To see those fundamentals applied to specific, industry-standard tools, continue on to:

* **[Docker](https://docs.docker.com)** — build and run individual containers on a single machine; the most common starting point for learning containers hands-on.

* **[Kubernetes](https://kubernetes.io)** — orchestrate many containers across many machines; the industry-standard tool for running containers in production.

## Table of Contents

**Basics**

   1. **[What Are Containers?](./[1]-What-Are-Containers.md)**  
       1.1 Defining a Container  
       1.2 Why Containers Exist  
       1.3 Key Benefits  
   2. **[Containers Vs Virtual Machines](./[2]-Containers-Vs-Virtual-Machines.md)**  
       2.1 How VMs Work  
       2.2 How Containers Work  
       2.3 Comparing Resource Usage  
       2.4 When to Use Which  
   3. **[Container Images And Layers](./[3]-Container-Images-And-Layers.md)**  
       3.1 What Is a Container Image  
       3.2 Layers and Caching  
       3.3 Base Images  
       3.4 Image Registries  
   4. **[The Container Lifecycle](./[4]-The-Container-Lifecycle.md)**  
       4.1 Build  
       4.2 Ship  
       4.3 Run  
       4.4 Stop and Remove  

**Ecosystem**

   5. **[Container Runtimes And Orchestration](./[5]-Container-Runtimes-And-Orchestration.md)**  
       5.1 What Is a Container Runtime  
       5.2 Single Host vs Multi Host  
       5.3 What Is Orchestration  
       5.4 Why Orchestration Matters at Scale  
   6. **[Choosing A Container Tool](./[6]-Choosing-A-Container-Tool.md)**  
       6.1 Matching Tools to Tasks  
       6.2 Docker for Local Development  
       6.3 Kubernetes for Production Orchestration  
       6.4 Where to Go Next

## Container Tools

The lessons above teach the fundamentals that apply across the whole container ecosystem. To see those fundamentals applied to specific, industry-standard tools, continue on to:

* **[Docker](https://docs.docker.com)** — build and run individual containers on a single machine; the most common starting point for learning containers hands-on.

* **[Kubernetes](https://kubernetes.io)** — orchestrate many containers across many machines; the industry-standard tool for running containers in production.
