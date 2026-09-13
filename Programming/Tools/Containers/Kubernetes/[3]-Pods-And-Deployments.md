[Previous](./[2]-Kubernetes-Architecture.md) | [Table of Contents](./[0]-Introduction-to-Kubernetes.md) | [Next](./[4]-Services-And-Networking.md)

*Core Concepts*

# Lesson 3 - Pods And Deployments

## 3.1 What Is a Pod

A **Pod** is the smallest unit Kubernetes schedules and runs — not a container directly. Most Pods contain a single container, but a Pod can hold multiple tightly-coupled containers that need to share storage and a network address (for example, a main application container and a small "sidecar" that ships its logs).

Pods are also **disposable**: Kubernetes doesn't try to keep a specific Pod alive forever. If a Pod dies, Kubernetes creates a brand new one to replace it — this is why you rarely create Pods directly in practice, and instead use a Deployment (3.4) to manage them.

## 3.2 Writing a Pod Manifest

Kubernetes resources are described in YAML manifests. Here's a minimal Pod manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
spec:
  containers:
    - name: my-app
      image: yourusername/my-app:1.0
      ports:
        - containerPort: 3000
```

Every manifest shares the same basic shape:

| Field | Meaning |
|---|---|
| `apiVersion` | Which version of the Kubernetes API this resource uses |
| `kind` | What type of resource this is (Pod, Deployment, Service, etc.) |
| `metadata` | Identifying information, like the resource's name |
| `spec` | The desired state — what the resource should actually contain or do |

You could run this directly with `kubectl apply -f my-app-pod.yaml`, but if that Pod crashes, nothing recreates it — which is exactly the gap Deployments fill.

## 3.3 ReplicaSets

A **ReplicaSet** is a controller that ensures a specified number of identical Pods are running at all times. If a Pod managed by a ReplicaSet is deleted or crashes, the ReplicaSet immediately creates a new one to bring the count back up.

In practice, you almost never create a ReplicaSet directly — instead, you create a Deployment, which manages ReplicaSets for you automatically.

## 3.4 Deployments

A **Deployment** is the resource you'll use most often. It manages a ReplicaSet on your behalf and adds features on top, like rolling updates (replacing old Pods with new ones gradually, without downtime).

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-app
          image: yourusername/my-app:1.0
          ports:
            - containerPort: 3000
```

- `replicas: 3` tells Kubernetes to always keep 3 identical Pods running.
- `selector` and the `labels` under `template` tell the Deployment which Pods belong to it.
- `template` describes the Pod itself — notice it looks just like the standalone Pod manifest from 3.2, nested inside.

Apply it the same way:

```bash
kubectl apply -f my-app-deployment.yaml
kubectl get deployments
kubectl get pods
```

> 💡 **Analogy:** A Pod is a single food truck. A ReplicaSet is the rule "there must always be exactly 3 trucks parked on this street." A Deployment is the manager who enforces that rule *and* knows how to swap in updated trucks one at a time without ever leaving the street empty.

### Quick check: Pod, ReplicaSet, or Deployment?

| Statement | Which one? |
|---|---|
| "The smallest thing that actually runs containers" | Pod |
| "Ensures N identical Pods are always running" | ReplicaSet |
| "What you create in practice; manages rolling updates too" | Deployment |

With Pods running reliably, the next lesson covers how other things — including users — actually reach them, since Pods come and go and don't have stable addresses on their own.

---

[Previous](./[2]-Kubernetes-Architecture.md) | [Table of Contents](./[0]-Introduction-to-Kubernetes.md) | [Next](./[4]-Services-And-Networking.md)
