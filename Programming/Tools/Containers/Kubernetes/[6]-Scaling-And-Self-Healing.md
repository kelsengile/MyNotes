[Previous](./[5]-ConfigMaps-And-Secrets.md) | [Table of Contents](./[0]-Introduction-to-Kubernetes.md)

*Core Concepts*

# Lesson 6 - Scaling And Self Healing

## 6.1 Manual and Automatic Scaling

Scaling a Deployment manually is a one-line change:

```bash
kubectl scale deployment my-app-deployment --replicas=10
```

For workloads with variable traffic, Kubernetes can also scale **automatically** with a Horizontal Pod Autoscaler (HPA), which adjusts the replica count based on metrics like CPU usage:

```bash
kubectl autoscale deployment my-app-deployment --min=3 --max=10 --cpu-percent=70
```

This tells Kubernetes: never run fewer than 3 replicas or more than 10, and add more whenever average CPU usage climbs above 70%.

## 6.2 Liveness and Readiness Probes

For Kubernetes to know *when* to restart or reroute traffic away from a Pod, it needs a way to check that Pod's health. **Probes** do exactly this:

| Probe | Question it answers | What happens on failure |
|---|---|---|
| **Liveness probe** | "Is this container still working?" | The container is restarted |
| **Readiness probe** | "Is this container ready to receive traffic?" | The Pod is temporarily removed from Service routing |

```yaml
spec:
  containers:
    - name: my-app
      image: yourusername/my-app:1.0
      livenessProbe:
        httpGet:
          path: /healthz
          port: 3000
        periodSeconds: 10
      readinessProbe:
        httpGet:
          path: /ready
          port: 3000
        periodSeconds: 5
```

Here, Kubernetes checks `/healthz` every 10 seconds to decide whether to restart the container, and `/ready` every 5 seconds to decide whether it should currently receive traffic from the Service.

## 6.3 How Self-Healing Works

Self-healing in Kubernetes comes from combining several pieces you've already learned in this Topic:

1. A **Deployment** declares how many replicas should exist.
2. The **Controller Manager** continuously compares that desired count to reality.
3. **Probes** tell Kubernetes when a specific Pod is unhealthy.
4. If a Pod fails a liveness probe, crashes, or its node goes down entirely, the Controller Manager notices the replica count has dropped and schedules a replacement Pod — with no human intervention required.

> 💡 **Analogy:** This is the dispatch system from Lesson 1 in full effect — probes are the checkups the dispatcher performs on every truck, and the moment one fails a checkup or vanishes off the map, a replacement is already on its way.

## 6.4 Working With `kubectl`

A few `kubectl` commands cover the vast majority of day-to-day work with a cluster:

| Command | What it does |
|---|---|
| `kubectl apply -f file.yaml` | Creates or updates a resource from a manifest |
| `kubectl get pods` / `deployments` / `services` | Lists resources of a given type |
| `kubectl describe pod <name>` | Shows detailed information and recent events for a resource |
| `kubectl logs <pod-name>` | Shows a Pod's container logs |
| `kubectl delete -f file.yaml` | Removes the resources defined in a manifest |

### Quick check: liveness or readiness?

| Scenario | Probe |
|---|---|
| App is still starting up and shouldn't get traffic yet | Readiness |
| App has deadlocked and needs a restart | Liveness |
| App is temporarily busy reindexing data | Readiness |

That completes the core Kubernetes workflow: describing Pods and Deployments, exposing them with Services, configuring them with ConfigMaps and Secrets, and letting Kubernetes scale and heal them automatically. From here, the best next step is hands-on practice — try turning the Docker Compose file from the Docker Topic into an equivalent set of Kubernetes manifests.

---

[Previous](./[5]-ConfigMaps-And-Secrets.md) | [Table of Contents](./[0]-Introduction-to-Kubernetes.md)
