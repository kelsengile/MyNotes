[⬅ Back to Container Fundamentals](../[0]-Introduction-to-Containers.md)

# Introduction to Kubernetes

Kubernetes (often abbreviated **K8s**) is the industry-standard tool for running containers in production, at scale, across many machines. Where Docker focuses on building and running a container on a single machine, Kubernetes focuses on a much harder problem: keeping hundreds or thousands of containers running reliably across a whole fleet of servers, automatically restarting them when they fail, scaling them up or down based on demand, and rolling out updates without downtime.

This Topic assumes familiarity with the Docker Topic — Kubernetes doesn't replace Docker's images or containers, it orchestrates them.

Download: [kubectl (Kubernetes command-line tool)](https://kubernetes.io/docs/tasks/tools/)

## Why Learn Kubernetes?

- **It's the de facto standard for container orchestration** — nearly every major cloud provider (AWS, Google Cloud, Azure) offers a managed Kubernetes service.
- **It's self-healing by design** — if a container crashes or a server goes down, Kubernetes automatically reschedules the affected workloads elsewhere.
- **It scales with a single command** — going from 3 copies of an app to 300 is a one-line change, not a manual, error-prone process.
- **It describes infrastructure as code** — a Kubernetes setup is written as YAML files that can be version-controlled, reviewed, and reused, rather than a series of manual steps.

A quick sense of how the two tools relate:

| Aspect | Docker | Kubernetes |
|---|---|---|
| Scope | A single machine | A cluster of many machines |
| Primary unit | Container | Pod (one or more containers) |
| Scaling | Manual | Automatic, based on rules you define |
| Self-healing | No — you restart things yourself | Yes — built in |
| Typical use | Local development, small deployments | Production workloads at scale |

## Table of Contents

**Getting Started**

   1. **[What Is Kubernetes?](./[1]-What-Is-Kubernetes.md)**  
       1.1 The Problem Kubernetes Solves  
       1.2 Declarative Configuration  
       1.3 Key Terminology  
   2. **[Kubernetes Architecture](./[2]-Kubernetes-Architecture.md)**  
       2.1 Clusters, Nodes, and the Control Plane  
       2.2 Control Plane Components  
       2.3 Node Components  
       2.4 How a Request Flows Through the Cluster  

**Core Concepts**

   3. **[Pods And Deployments](./[3]-Pods-And-Deployments.md)**  
       3.1 What Is a Pod  
       3.2 Writing a Pod Manifest  
       3.3 ReplicaSets  
       3.4 Deployments  
   4. **[Services And Networking](./[4]-Services-And-Networking.md)**  
       4.1 Why Pods Need Services  
       4.2 Service Types  
       4.3 Writing a Service Manifest  
       4.4 DNS Inside the Cluster  
   5. **[ConfigMaps And Secrets](./[5]-ConfigMaps-And-Secrets.md)**  
       5.1 Separating Configuration From Code  
       5.2 ConfigMaps  
       5.3 Secrets  
       5.4 Using Them in a Pod  
   6. **[Scaling And Self Healing](./[6]-Scaling-And-Self-Healing.md)**  
       6.1 Manual and Automatic Scaling  
       6.2 Liveness and Readiness Probes  
       6.3 How Self-Healing Works  
       6.4 Working With `kubectl`
