[Previous](./[3]-Docker-Images-And-Dockerfiles.md) | [Table of Contents](./[0]-Introduction-to-Docker.md) | [Next](./[5]-Docker-Volumes-And-Networking.md)

*Working With Docker*

# Lesson 4 - Running And Managing Containers

## 4.1 Starting a Container

The core command for starting a container is `docker run`, followed by an image name:

```bash
docker run my-app:1.0
```

This creates a new container from the `my-app:1.0` image and starts it. If the image isn't available locally, Docker automatically pulls it from a registry first.

## 4.2 Common `docker run` Flags

Most real-world usage adds flags to control how the container behaves:

| Flag | What it does |
|---|---|
| `-d` | Runs the container in detached mode (in the background) |
| `-p HOST:CONTAINER` | Maps a port on your machine to a port inside the container |
| `--name` | Gives the container a friendly name instead of a random one |
| `-e KEY=VALUE` | Sets an environment variable inside the container |
| `-v` | Mounts a volume or bind mount (covered in the next lesson) |
| `--rm` | Automatically removes the container when it stops |

A common combination for running a web app in the background:

```bash
docker run -d -p 8080:3000 --name my-app-container my-app:1.0
```

This runs the container in the background, and maps port `8080` on your machine to port `3000` inside the container — so visiting `http://localhost:8080` reaches the app.

## 4.3 Inspecting Running Containers

Once a container is running, several commands help you see what's happening:

```bash
# List running containers
docker ps

# List all containers, including stopped ones
docker ps -a

# View a container's logs
docker logs my-app-container

# Stream logs live
docker logs -f my-app-container

# Open a shell inside a running container
docker exec -it my-app-container sh
```

> 💡 **Tip:** `docker exec -it` is one of the most useful debugging tools available — it lets you poke around inside a running container exactly as if you had SSH'd into it.

## 4.4 Stopping and Removing Containers

Containers can be stopped and removed independently of their images:

```bash
# Stop a running container
docker stop my-app-container

# Start it again later
docker start my-app-container

# Remove a stopped container
docker rm my-app-container

# Remove an image (must not be in use by a container)
docker rmi my-app:1.0
```

### Quick check: which command?

| Goal | Command |
|---|---|
| See containers currently running | `docker ps` |
| Run a container in the background | `docker run -d ...` |
| Get a shell inside a running container | `docker exec -it ... sh` |
| Permanently delete a stopped container | `docker rm` |

You now know the full lifecycle of a single container. The next lesson covers what happens to a container's data when it stops — and how to keep it around using volumes.

---

[Previous](./[3]-Docker-Images-And-Dockerfiles.md) | [Table of Contents](./[0]-Introduction-to-Docker.md) | [Next](./[5]-Docker-Volumes-And-Networking.md)
