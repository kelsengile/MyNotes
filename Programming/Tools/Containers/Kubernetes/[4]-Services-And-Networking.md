[Previous](./[3]-Pods-And-Deployments.md) | [Table of Contents](./[0]-Introduction-to-Kubernetes.md) | [Next](./[5]-ConfigMaps-And-Secrets.md)

*Core Concepts*

# Lesson 4 - Services And Networking

## 4.1 Why Pods Need Services

Every Pod gets its own IP address — but that address is not stable. When a Pod crashes and is replaced, the new Pod gets a *new* IP address. If other parts of your application (or outside users) tried to connect directly to a Pod's IP, that connection would break every time a Pod restarts.

A **Service** solves this by giving a stable, unchanging address that automatically routes traffic to whichever healthy Pods currently match a given label — regardless of how many times those Pods are replaced underneath it.

## 4.2 Service Types

| Type | What it does |
|---|---|
| **ClusterIP** (default) | Exposes the Service only inside the cluster |
| **NodePort** | Exposes the Service on a static port on every node's IP, reachable from outside the cluster |
| **LoadBalancer** | Provisions an external load balancer (typically via a cloud provider) that routes to the Service |

> 💡 **Tip:** `ClusterIP` is the right default for internal communication (e.g. an app talking to a database). `LoadBalancer` is typically used for the entry point of a public-facing app, when running on a cloud provider that supports it.

## 4.3 Writing a Service Manifest

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app-service
spec:
  type: ClusterIP
  selector:
    app: my-app
  ports:
    - port: 80
      targetPort: 3000
```

- `selector` tells the Service which Pods to send traffic to — here, any Pod labeled `app: my-app` (matching the Deployment's `template` labels from the previous lesson).
- `port` is the port the Service itself listens on; `targetPort` is the port the traffic gets forwarded to on the matched Pods.

Apply and inspect it the same way as other resources:

```bash
kubectl apply -f my-app-service.yaml
kubectl get services
```

## 4.4 DNS Inside the Cluster

Kubernetes runs an internal DNS system that automatically gives every Service a resolvable hostname, matching its `metadata.name`. This means one Pod can reach another Pod's Service simply by name — no hardcoded IP addresses required:

```
   Pod (frontend)  ──▶  http://my-app-service  ──▶  routed to a healthy Pod
```

This mirrors the container-name-based DNS you saw with Docker networks in the Docker Topic — Kubernetes just applies the same idea across an entire cluster instead of a single machine.

### Quick check: which Service type?

| Need | Service type |
|---|---|
| An internal database only other Pods should reach | ClusterIP |
| A public-facing app on a cloud provider | LoadBalancer |
| Quick external access without a cloud load balancer | NodePort |

Services solve *how* Pods are reached — the next lesson covers *what* configuration and secrets those Pods actually run with.

---

[Previous](./[3]-Pods-And-Deployments.md) | [Table of Contents](./[0]-Introduction-to-Kubernetes.md) | [Next](./[5]-ConfigMaps-And-Secrets.md)
