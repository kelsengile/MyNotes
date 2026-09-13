[Previous](./[4]-Services-And-Networking.md) | [Table of Contents](./[0]-Introduction-to-Kubernetes.md) | [Next](./[6]-Scaling-And-Self-Healing.md)

*Core Concepts*

# Lesson 5 - ConfigMaps And Secrets

## 5.1 Separating Configuration From Code

Baking configuration directly into a container image is a bad practice — it means rebuilding the image just to change a setting, and it makes it impossible to reuse the same image across different environments (development, staging, production). Kubernetes solves this with two resources that inject configuration into Pods at runtime: **ConfigMaps** and **Secrets**.

## 5.2 ConfigMaps

A **ConfigMap** stores non-sensitive configuration as key-value pairs, kept separate from your application's image.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-app-config
data:
  LOG_LEVEL: "info"
  MAX_CONNECTIONS: "100"
```

## 5.3 Secrets

A **Secret** is structurally almost identical to a ConfigMap, but is meant for sensitive values — passwords, API keys, tokens. Values are stored base64-encoded (not encrypted by default, so most production clusters combine Secrets with additional encryption or an external secrets manager).

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-app-secret
type: Opaque
data:
  DATABASE_PASSWORD: c3VwZXJzZWNyZXQ=
```

> 💡 **Tip:** The value above is just the base64 encoding of `supersecret` — you can produce it yourself with `echo -n 'supersecret' | base64`. Base64 is an encoding, not encryption, so Secrets alone shouldn't be treated as fully secure storage for highly sensitive data.

## 5.4 Using Them in a Pod

Both ConfigMaps and Secrets are typically consumed as environment variables inside a Pod's containers:

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
          env:
            - name: LOG_LEVEL
              valueFrom:
                configMapKeyRef:
                  name: my-app-config
                  key: LOG_LEVEL
            - name: DATABASE_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: my-app-secret
                  key: DATABASE_PASSWORD
```

This Deployment reaches into both the ConfigMap and the Secret created above, and exposes their values as environment variables inside every replica — without any of that configuration living inside the container image itself.

### Quick check: ConfigMap or Secret?

| Data | Which resource? |
|---|---|
| A logging verbosity setting | ConfigMap |
| A database password | Secret |
| A feature flag toggle | ConfigMap |
| A third-party API key | Secret |

With Pods, Services, and configuration covered, the final lesson looks at how Kubernetes scales applications and keeps them healthy automatically.

---

[Previous](./[4]-Services-And-Networking.md) | [Table of Contents](./[0]-Introduction-to-Kubernetes.md) | [Next](./[6]-Scaling-And-Self-Healing.md)
