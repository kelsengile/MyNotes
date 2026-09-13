[Previous](./[0]-Introduction-to-Docker.md) | [Table of Contents](./[0]-Introduction-to-Docker.md) | [Next](./[2]-Installing-Docker.md)

*Getting Started*

# Lesson 1 - What Is Docker

## 1.1 Docker's Role in the Container Ecosystem

Docker is a platform for building, running, and sharing containers. It isn't the only way to work with containers, but it was the tool that popularized them, and its image format became so widely used that it's now supported as an open standard (the OCI, or Open Container Initiative, image format) that other tools — including Kubernetes — can run directly.

Concretely, Docker gives you three things:

- A way to **build** a container image from a simple text file (a `Dockerfile`).
- A way to **run** that image as a container on your machine.
- A way to **share** that image with others through a registry, most commonly Docker Hub.

> 💡 **Analogy:** If a container is a shipping container, Docker is the crane, the ship, and the paperwork all in one — it's what lets you pack the container, load it, move it anywhere, and unload it, without you needing to build any of that infrastructure yourself.

## 1.2 The Docker Engine and CLI

Docker is made up of a few cooperating pieces:

| Component | What it does |
|---|---|
| Docker Engine (daemon) | The background service that actually builds images and runs containers |
| Docker CLI | The `docker` command you type in your terminal, which talks to the Engine |
| Docker Desktop | A packaged app (Windows/macOS) that bundles the Engine, CLI, and a GUI |
| Docker Hub | A public registry where images can be stored and downloaded |

When you type a command like `docker run nginx`, the CLI sends that request to the Engine, which does the actual work of pulling the image and starting the container.

## 1.3 Docker's Core Objects

Almost everything you do in Docker revolves around four objects:

- **Image** — a read-only template containing an application and everything it needs to run. Think of it as a snapshot or blueprint.
- **Container** — a running (or stopped) instance of an image. You can run many containers from the same image.
- **Volume** — a mechanism for persisting data outside of a container's own filesystem, so it survives even if the container is removed.
- **Network** — a virtual network that lets containers communicate with each other and with the outside world.

```
   Dockerfile  ──build──▶  Image  ──run──▶  Container
                              │
                              └─push/pull─▶  Registry (e.g. Docker Hub)
```

### Quick check: image or container?

| Statement | Image or Container? |
|---|---|
| "A read-only blueprint you can run many times" | Image |
| "A running process created from a blueprint" | Container |
| "Stored on Docker Hub for others to download" | Image |
| "Can be started, stopped, and removed individually" | Container |

Understanding this image-vs-container distinction is the single most important mental model for everything else in this Topic — the next lesson gets Docker installed so you can start building and running both.

---

[Previous](./[0]-Introduction-to-Docker.md) | [Table of Contents](./[0]-Introduction-to-Docker.md) | [Next](./[2]-Installing-Docker.md)
