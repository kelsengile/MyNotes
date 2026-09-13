[Previous](./[5]-Docker-Volumes-And-Networking.md) | [Table of Contents](./[0]-Introduction-to-Docker.md)

*Working With Docker*

# Lesson 6 - Docker Compose

## 6.1 Why Docker Compose Exists

Most real applications aren't a single container — a typical web app might need a backend, a database, and a cache, each running in its own container, all wired together with the right volumes, networks, and environment variables. Starting each one manually with `docker run` flags quickly becomes hard to reproduce and easy to get wrong.

**Docker Compose** solves this by letting you describe an entire multi-container application in a single YAML file, then bring the whole thing up (or down) with one command.

## 6.2 Writing a `docker-compose.yml` File

```yaml
version: "3.9"

services:
  app:
    build: .
    ports:
      - "8080:3000"
    environment:
      - DATABASE_URL=postgres://db:5432/mydb
    depends_on:
      - db

  db:
    image: postgres:16
    volumes:
      - db-data:/var/lib/postgresql/data
    environment:
      - POSTGRES_PASSWORD=example

volumes:
  db-data:
```

This single file replaces multiple manual `docker run`, `docker volume create`, and `docker network create` commands:

- `services` defines each container to run — here, `app` (built from the local Dockerfile) and `db` (using a pre-built Postgres image).
- `depends_on` ensures `db` starts before `app`.
- `volumes` declares a named volume so the database's data persists across restarts.
- Compose automatically creates a shared network so `app` can reach `db` by name, just like in the previous lesson.

## 6.3 Common Compose Commands

| Command | What it does |
|---|---|
| `docker compose up` | Builds (if needed) and starts all services |
| `docker compose up -d` | Same, but in detached (background) mode |
| `docker compose down` | Stops and removes all services and their network |
| `docker compose ps` | Lists the services and their status |
| `docker compose logs -f` | Streams logs from all services |

> 💡 **Tip:** Add `-d` to `docker compose up` the same way you would with `docker run`, to keep your terminal free while everything runs in the background.

## 6.4 From Compose to Kubernetes

Docker Compose is excellent for local development and small deployments on a single machine, but it isn't designed to manage containers across *multiple* machines, automatically restart failed services in a fleet, or scale an application up and down based on demand.

That's exactly the gap Kubernetes fills. The concepts you've learned here carry over directly:

| Docker Compose concept | Rough Kubernetes equivalent |
|---|---|
| A `service` in `docker-compose.yml` | A Deployment + Service |
| `docker compose up` | Applying a set of manifests with `kubectl apply` |
| A named volume | A PersistentVolume |
| `environment` variables | ConfigMaps and Secrets |

Continue to the **[Introduction to Kubernetes](../Kubernetes/[0]-Introduction-to-Kubernetes.md)** to see how these same ideas scale up to production.

---

[Previous](./[5]-Docker-Volumes-And-Networking.md) | [Table of Contents](./[0]-Introduction-to-Docker.md)
