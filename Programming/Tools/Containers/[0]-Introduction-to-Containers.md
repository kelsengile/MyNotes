[⬅ Back to README](../../../README.md)

# Introduction to Containers

Containers are a way of packaging an application together with everything it needs to run — code, runtime, system libraries, and configuration — so that it behaves the same way no matter where it's deployed. This Topic covers the fundamental, tool-agnostic concepts behind containers: what they are, how they differ from virtual machines, how container images work, and how containers are run and orchestrated in practice.

These lessons intentionally avoid tying the concepts to any single tool. Once you understand the fundamentals here, head over to the tool-specific folders below to see how those concepts are put into practice with real, widely-used tools.

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

- **[Docker](./Docker/[0]-Introduction-to-Docker.md)** — build and run individual containers on a single machine; the most common starting point for learning containers hands-on.
- **[Kubernetes](./Kubernetes/[0]-Introduction-to-Kubernetes.md)** — orchestrate many containers across many machines; the industry-standard tool for running containers in production.
