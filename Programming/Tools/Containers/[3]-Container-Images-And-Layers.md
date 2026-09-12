[Previous](./[2]-Containers-Vs-Virtual-Machines.md) | [Table of Contents](./[0]-Introduction-to-Containers.md) | [Next](./[4]-The-Container-Lifecycle.md)

*Basics*

# Lesson 3 - Container Images And Layers

## 3.1 What Is a Container Image

A container image is a read-only template that contains everything needed to create a container: application code, a runtime, libraries, environment variables, and configuration files. When you "run" a container, you're really starting a live, writable instance of an image.

Images are immutable — running a container doesn't change the underlying image. This makes images predictable and safe to reuse: the same image will always produce a container that starts out in exactly the same state.

> 💡 **Analogy:** An image is like a cookie cutter, and a running container is one cookie cut from it. You can stamp out as many identical cookies as you like — the cutter itself never changes, no matter how many cookies you make.

```
   Image (read-only)              Container (running)
   ┌─────────────────┐            ┌─────────────────┐
   │  App + deps      │  ──run──▶  │  App + deps      │
   │  (immutable)      │            │  + writable layer │
   └─────────────────┘            └─────────────────┘
        one image          →     many independent containers
```

## 3.2 Layers and Caching

Images are built as a stack of layers, where each layer represents a set of filesystem changes — for example, installing a package, or copying in application code. Layers are stacked on top of one another to form the final image.

This layered structure has a practical benefit: layers can be cached and reused. If only the top layer of an image changes (say, an application code update), the layers underneath don't need to be rebuilt or re-downloaded, which makes building and distributing images much faster.

```
┌───────────────────────────┐
│ Layer 4: Application code  │  ← changes often
├───────────────────────────┤
│ Layer 3: App dependencies  │  ← changes occasionally
├───────────────────────────┤
│ Layer 2: Language runtime  │  ← changes rarely
├───────────────────────────┤
│ Layer 1: Base OS files     │  ← changes almost never
└───────────────────────────┘
```

**Why order matters:** tools that build images (like Docker) cache each layer. If you put things that rarely change (the base OS, the runtime) at the bottom and things that change often (your app code) at the top, only the top layers need rebuilding after a code change — the rest are reused instantly from cache.

> 💡 **Analogy:** It's like packing a moving truck with heavy furniture at the bottom and fragile, frequently-needed boxes near the door. If you only need to grab one box, you don't want to unpack the whole truck to get to it.

## 3.3 Base Images

Most images start from a **base image** — a starting point that already contains an operating system's core files or a language runtime. Application-specific layers are then added on top of this base.

Choosing a good base image matters: smaller, well-maintained base images generally lead to smaller, more secure final images, since there's less unnecessary software included.

| Base image style | Typical size | Trade-off |
|---|---|---|
| Full OS distribution | 100s of MB | Familiar, lots of tools, larger attack surface |
| Slim/minimal variant | Tens of MB | Smaller and faster, fewer built-in tools |
| "Distroless" / scratch | A few MB or less | Smallest and most secure, hardest to debug inside |

## 3.4 Image Registries

Once built, images are typically stored in a **registry** — a service that hosts images so they can be shared and downloaded ("pulled") by anyone who needs to run them. Registries can be public, for freely sharing open-source images, or private, for storing an organization's proprietary images securely.

Pulling an image from a registry and running it is what allows the same tested, packaged application to be deployed consistently across many different machines.

> 💡 **Analogy:** A registry is like an app store for images — publish once, and anyone with access can download ("pull") the exact same package and run it, whether that's a public app store for open-source tools or a private, internal one for company software.

```
Developer's        push        Registry         pull        Production
 machine    ────────────────▶  (stores      ──────────────▶   server
 (builds                        images)                       (runs the
  image)                                                       same image)
```

An image is typically referenced by **name** and **tag**, for example `my-app:1.4` — the name identifies *what* it is, and the tag identifies *which version*.

---

[Previous](./[2]-Containers-Vs-Virtual-Machines.md) | [Table of Contents](./[0]-Introduction-to-Containers.md) | [Next](./[4]-The-Container-Lifecycle.md)
