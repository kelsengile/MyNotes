[Previous](./[24]-Container-Registries.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[26]-Managed-Kubernetes-Services.md)

*Containers & Orchestration*

# Lesson 25 - Kubernetes Fundamentals

## 25.1 Kubernetes Architecture

**Kubernetes** (often abbreviated "K8s") is the industry-standard container orchestration platform, originally developed by Google. A Kubernetes **cluster** consists of a **control plane** (which makes scheduling decisions and maintains the cluster's desired state) and **worker nodes** (machines that actually run your containers). You describe your application's desired state in configuration files, and Kubernetes continuously works to keep the real cluster matching that description — restarting failed containers, rescheduling them onto healthy nodes, and more.

---

## 25.2 Pods, Deployments, Services

Three core Kubernetes objects to understand first:

- **Pod** — the smallest deployable unit; wraps one or more tightly-coupled containers that share networking and storage. Pods are ephemeral and disposable.
- **Deployment** — describes how many replicas of a pod should run and how updates should be rolled out; Kubernetes uses it to keep the right number of healthy pods running at all times.
- **Service** — provides a stable network endpoint (a consistent name/IP) in front of a changing set of pods, since individual pods come and go and their IPs are not stable.

Example minimal Deployment:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
spec:
  replicas: 3
  selector:
    matchLabels: { app: web }
  template:
    metadata:
      labels: { app: web }
    spec:
      containers:
        - name: web
          image: myregistry.example.com/my-app:1.0
          ports: [{ containerPort: 3000 }]
```

---

## 25.3 kubectl Basics

`kubectl` is Kubernetes' CLI for interacting with a cluster:

```bash
kubectl apply -f deployment.yaml     # create/update resources from a file
kubectl get pods                     # list running pods
kubectl logs <pod-name>              # view a pod's logs
kubectl describe pod <pod-name>      # detailed status and recent events
kubectl scale deployment web --replicas=5   # manually scale
```

Kubernetes' declarative model means `kubectl apply` is idempotent: reapplying the same file repeatedly converges the cluster to that state rather than duplicating resources, similar in spirit to Terraform (Lesson 21) but focused specifically on containerized workloads.

---

[Previous](./[24]-Container-Registries.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[26]-Managed-Kubernetes-Services.md)
