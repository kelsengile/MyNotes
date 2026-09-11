[Previous](./[3]-Container-Images-And-Layers.md) | [Table of Contents](./[0]-Introduction-to-Containers.md) | [Next](./[5]-Container-Runtimes-And-Orchestration.md)

*Basics*

# Lesson 4 - The Container Lifecycle

```
   ┌───────┐    ┌───────┐    ┌───────┐    ┌────────────────┐
   │ Build │ ─▶ │  Ship │ ─▶ │  Run  │ ─▶ │ Stop / Remove   │
   └───────┘    └───────┘    └───────┘    └────────────────┘
   Dockerfile    Push to      Start a       Halt or delete
   → Image       Registry     container     the container
```

## 4.1 Build

Everything starts with a build step, where instructions describing an image — such as which base image to start from, which files to copy in, and which commands to run — are turned into an actual image made up of layers, as covered in the previous lesson. The result is a reusable artifact that can be stored and shared.

**Example build instructions (tool-agnostic pseudocode):**

```text
FROM base-image:version
COPY ./app /app
RUN install-dependencies
CMD start-the-app
```

Each line above becomes one layer in the resulting image.

## 4.2 Ship

Once built, an image needs to get to wherever it will run. This usually means pushing it to a registry, then pulling it down on the target machine. "Shipping" an image is what separates *building* software from *deploying* it, and it's what allows the exact same tested image to move from a developer's machine to a test environment to production without changes.

## 4.3 Run

Running a container takes a read-only image and starts a live, writable instance of it as an isolated process on the host. While running, a container can read and write its own filesystem, communicate over the network, and be given resource limits like how much memory or CPU it's allowed to use.

Multiple containers can be started from the same image at the same time, each one isolated from the others, which is what makes it easy to scale an application by simply running more copies of it.

```
        image: my-app:1.0
              │
   ┌──────────┼──────────┐
   ▼          ▼          ▼
container-1 container-2 container-3
 (isolated)  (isolated)  (isolated)
```

## 4.4 Stop and Remove

A container can be stopped, which halts its running process but keeps its writable state around, or removed entirely, which deletes that state along with it. Because containers are meant to be disposable, it's common practice to design applications so that important data lives outside the container — for example, in a separate storage volume — so nothing important is lost when a container is stopped or removed.

This disposability is a deliberate design choice: it should always be safe to throw away a container and start a fresh one from the same image.

### Lifecycle states at a glance

| State | Meaning | Can you recover its data? |
|---|---|---|
| Running | Process is active | N/A — still running |
| Stopped | Process halted, writable layer kept | Yes, until removed |
| Removed | Writable layer deleted | No — unless stored in an external volume |

---

[Previous](./[3]-Container-Images-And-Layers.md) | [Table of Contents](./[0]-Introduction-to-Containers.md) | [Next](./[5]-Container-Runtimes-And-Orchestration.md)
