[Previous](./[0]-Introduction-to-Containers.md) | [Table of Contents](./[0]-Introduction-to-Containers.md) | [Next](./[2]-Containers-Vs-Virtual-Machines.md)

*Basics*

# Lesson 1 - What Are Containers

## 1.1 Defining a Container

A container is a lightweight, standalone package that bundles an application's code together with everything it needs to run: system libraries, dependencies, and configuration files. Once packaged, a container behaves the same way on a developer's laptop, a test server, or a production machine, because it carries its own environment with it instead of relying on whatever happens to be installed on the host.

Containers are isolated from one another and from the host system using features built into the operating system's kernel, but unlike a full virtual machine, they share that same kernel rather than running their own copy of an operating system.

```
┌─────────────────────────────────────────────────┐
│                   Host Machine                   │
│  ┌───────────┐   ┌───────────┐   ┌───────────┐   │
│  │ Container │   │ Container │   │ Container │   │
│  │    A      │   │    B      │   │    C      │   │
│  │  (App +   │   │  (App +   │   │  (App +   │   │
│  │   deps)   │   │   deps)   │   │   deps)   │   │
│  └───────────┘   └───────────┘   └───────────┘   │
│  ──────────────────────────────────────────────  │
│              Shared Host OS Kernel                │
└─────────────────────────────────────────────────┘
```

## 1.2 Why Containers Exist

Before containers became common, teams often ran into the "it works on my machine" problem — code that ran fine in development but failed in production because of differences in installed software versions, missing dependencies, or operating system configuration.

Containers solve this by making the application's environment part of what gets shipped. If a container runs correctly in one place, it will run correctly anywhere that supports containers, because the environment travels with the application instead of being assembled separately at each destination.

**A concrete example:**

| Without containers | With containers |
|---|---|
| Dev has Python 3.11, prod has Python 3.9 → app crashes | Container ships Python 3.11 with the app → behaves identically everywhere |
| "Works on my machine" | "Works in my container" — and therefore everywhere |
| Manually documented setup steps, easy to drift | Setup is code, captured once, reproduced exactly |

## 1.3 Key Benefits

Containers offer several practical advantages for developers and teams:

- **Consistency** — the same container runs identically across development, testing, and production.
- **Isolation** — each container has its own filesystem and process space, so applications don't interfere with one another.
- **Efficiency** — containers share the host's kernel, so they start faster and use fewer resources than running a full separate operating system for each application.
- **Portability** — a container built on one machine can run on any other machine with a compatible container runtime installed.

### Quick check: is this a container concept?

| Statement | Container concept? |
|---|---|
| "Bundles app code with its dependencies" | ✅ Yes |
| "Runs its own full guest operating system" | ❌ No — that's a VM (next lesson) |
| "Shares the host machine's kernel" | ✅ Yes |
| "Starts in milliseconds rather than minutes" | ✅ Yes |

These benefits are what make containers a foundational tool in modern software development, and they set the stage for the comparison in the next lesson.

---

[Previous](./[0]-Introduction-to-Containers.md) | [Table of Contents](./[0]-Introduction-to-Containers.md) | [Next](./[2]-Containers-Vs-Virtual-Machines.md)
