[Previous](./[1]-What-Is-Kubernetes.md) | [Table of Contents](./[0]-Introduction-to-Kubernetes.md) | [Next](./[3]-Pods-And-Deployments.md)

*Getting Started*

# Lesson 2 - Kubernetes Architecture

## 2.1 Clusters, Nodes, and the Control Plane

A Kubernetes **cluster** is made up of two kinds of machines:

- The **control plane** — the "brain" of the cluster, which makes decisions about what should run where.
- **Worker nodes** — the machines that actually run your containers, inside Pods.

```
┌───────────────────────────────────────────────────────┐
│                     Control Plane                      │
│   API Server │ Scheduler │ Controller Manager │ etcd   │
└───────────────────────────────────────────────────────┘
                          │
          ┌───────────────┼───────────────┐
          ▼               ▼               ▼
   ┌─────────────┐ ┌─────────────┐ ┌─────────────┐
   │   Node 1    │ │   Node 2    │ │   Node 3    │
   │  kubelet    │ │  kubelet    │ │  kubelet    │
   │  Pods...    │ │  Pods...    │ │  Pods...    │
   └─────────────┘ └─────────────┘ └─────────────┘
```

## 2.2 Control Plane Components

| Component | Role |
|---|---|
| **API Server** | The front door to the cluster — every command, including from `kubectl`, goes through it |
| **etcd** | A key-value store holding the cluster's entire current state and configuration |
| **Scheduler** | Decides which node a new Pod should run on, based on resources and constraints |
| **Controller Manager** | Runs background loops that constantly compare desired state to actual state, and corrects drift |

In managed cloud offerings (like Google Kubernetes Engine or Amazon EKS), the control plane is run for you by the provider — you typically only manage worker nodes and the workloads on them.

## 2.3 Node Components

Each worker node runs a few key pieces of software:

| Component | Role |
|---|---|
| **kubelet** | An agent that talks to the API Server and makes sure the Pods assigned to this node are running correctly |
| **Container runtime** | The software that actually runs containers (e.g. containerd) — this is where Docker-built images get executed |
| **kube-proxy** | Handles networking rules so traffic can reach the right Pods |

## 2.4 How a Request Flows Through the Cluster

Putting it together, here's what happens when you ask Kubernetes to run something (e.g. via `kubectl apply`):

1. `kubectl` sends your manifest to the **API Server**.
2. The API Server validates it and stores the desired state in **etcd**.
3. The **Scheduler** notices a new Pod needs a home, and picks a suitable node.
4. The **kubelet** on that node is told to run the Pod, and pulls the container image via the **container runtime**.
5. The **Controller Manager** keeps watching — if the Pod later dies, this loop notices and starts a replacement.

> 💡 **Analogy:** The API Server is the front desk of a company, etcd is the filing cabinet holding every record, the Scheduler is HR deciding which office (node) a new hire (Pod) works from, and the kubelet is the office manager making sure that hire actually shows up and does their job every day.

### Quick check: which component?

| Task | Component |
|---|---|
| Stores the cluster's entire current state | etcd |
| Decides which node a new Pod runs on | Scheduler |
| Runs on every node, keeping its Pods healthy | kubelet |
| The only way `kubectl` talks to the cluster | API Server |

With the architecture in place, the next lesson introduces Pods and Deployments — the objects you'll actually create most often.

---

[Previous](./[1]-What-Is-Kubernetes.md) | [Table of Contents](./[0]-Introduction-to-Kubernetes.md) | [Next](./[3]-Pods-And-Deployments.md)
