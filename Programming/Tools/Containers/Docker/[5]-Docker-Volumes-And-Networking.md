[Previous](./[4]-Running-And-Managing-Containers.md) | [Table of Contents](./[0]-Introduction-to-Docker.md) | [Next](./[6]-Docker-Compose.md)

*Working With Docker*

# Lesson 5 - Docker Volumes And Networking

## 5.1 Why Containers Need Persistent Storage

By default, any data a container writes disappears the moment that container is removed — the container's filesystem is tied to its own lifecycle. This is fine for stateless apps, but a real problem for anything that needs to keep data around, like a database.

Docker solves this with **volumes**: storage that exists independently of any single container, so data survives container restarts and removals.

## 5.2 Volumes vs Bind Mounts

Docker offers two main ways to persist or share data with a container:

| Type | Where the data lives | Managed by Docker? |
|---|---|---|
| Volume | A location managed entirely by Docker | Yes |
| Bind mount | A specific folder on your host machine | No — you specify the exact path |

```bash
# Create and use a named volume
docker volume create app-data
docker run -d -v app-data:/var/lib/app-data my-app:1.0

# Use a bind mount to a local folder instead
docker run -d -v /home/user/app-data:/var/lib/app-data my-app:1.0
```

> 💡 **Analogy:** A volume is like renting a storage unit that the company manages for you — you don't need to know exactly where it is, just that it's reserved for your stuff. A bind mount is like using a specific drawer in your own house — you pick the exact location yourself.

Volumes are generally preferred for production data (like databases), while bind mounts are especially useful during development, since editing a file on your host machine is instantly reflected inside the container.

## 5.3 Docker Networking Basics

By default, every container gets its own network namespace, but Docker also creates a default **bridge network** that lets containers on the same host talk to each other. Custom networks give you more control over which containers can see each other:

```bash
# Create a custom network
docker network create my-network

# Run a container attached to it
docker run -d --network my-network --name db postgres

# List existing networks
docker network ls
```

## 5.4 Connecting Containers to Each Other

Containers on the same custom network can reach each other using their container name as a hostname — Docker handles the DNS resolution automatically:

```bash
docker run -d --network my-network --name db postgres
docker run -d --network my-network --name app my-app:1.0
```

Inside the `app` container, code can connect to the database simply by using `db` as the hostname (e.g. `db:5432`), without needing to know its actual IP address.

### Quick check: volume, bind mount, or network?

| Need | Solution |
|---|---|
| Persist database data across restarts | Volume |
| Live-edit code from your host machine | Bind mount |
| Let two containers talk to each other by name | Custom network |

Manually wiring up volumes and networks for every container gets tedious once an app has multiple pieces — that's exactly the problem the next lesson's tool, Docker Compose, solves.

---

[Previous](./[4]-Running-And-Managing-Containers.md) | [Table of Contents](./[0]-Introduction-to-Docker.md) | [Next](./[6]-Docker-Compose.md)
