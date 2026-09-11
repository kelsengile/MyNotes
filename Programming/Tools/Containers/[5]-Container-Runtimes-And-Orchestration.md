[Previous](./[4]-The-Container-Lifecycle.md) | [Table of Contents](./[0]-Introduction-to-Containers.md) | [Next](./[6]-Choosing-A-Container-Tool.md)

*Ecosystem*

# Lesson 5 - Container Runtimes And Orchestration

## 5.1 What Is a Container Runtime

A container runtime is the software responsible for actually running containers on a machine — pulling images, setting up isolation, starting and stopping processes, and managing their resources. It's the layer that turns an image into a running container, as described in the previous lesson.

Runtimes are typically an implementation detail: developers usually interact with a higher-level tool that talks to a runtime on their behalf, rather than working with the runtime directly.

```
 Developer-facing tool  ──talks to──▶  Container Runtime  ──manages──▶  Running Containers
   (e.g. Docker CLI)                  (does the actual isolation
                                        and process management)
```

## 5.2 Single Host vs Multi Host

Running a handful of containers on a single machine is straightforward — start them, stop them, check on them, all in one place. But real applications often need to run across many machines at once, whether for extra capacity, for resilience if one machine fails, or to spread traffic across regions.

Once containers are spread across multiple hosts, new problems appear: how do you decide which machine a container should run on, how do containers on different machines find and talk to each other, and what happens automatically if one crashes?

| | Single host | Multi-host |
|---|---|---|
| Placement | Trivial — only one option | Needs a scheduling decision |
| Failure recovery | Manual restart | Needs automatic detection + rescheduling |
| Networking | Local, simple | Needs cross-machine service discovery |
| Scaling | Limited to one machine's capacity | Can scale across the whole fleet |

## 5.3 What Is Orchestration

Orchestration is the practice of automatically managing containers across many machines: deciding where each container runs, restarting ones that fail, scaling the number of running copies up or down, and coordinating how they connect to each other over the network.

An orchestrator takes over decisions that would otherwise have to be made by hand, letting teams describe the *desired* state of their application — for example, "always keep three copies of this container running" — and having the system continuously work to make that state real.

```
 You declare:  "Keep 3 copies of my-app running, always"
                            │
                            ▼
                     ┌──────────────┐
                     │ Orchestrator │  ← constantly compares
                     │ (control     │     desired vs actual state
                     │  loop)       │
                     └──────────────┘
                            │
         ┌──────────────────┼──────────────────┐
         ▼                  ▼                  ▼
   container on         container on       container on
    Machine A             Machine B          Machine C
```

If `Machine B` crashes, the orchestrator notices only 2 copies are running, and automatically starts a replacement — without anyone stepping in.

## 5.4 Why Orchestration Matters at Scale

For a single container on a single machine, orchestration is overkill. But as the number of containers and machines grows, manually tracking what's running where quickly becomes unmanageable. Orchestration tools make it possible to reliably run large, distributed applications made up of many containers, without requiring a human to babysit every individual one.

This is the problem that led to the rise of dedicated orchestration tools, which you'll be introduced to in the next lesson.

---

[Previous](./[4]-The-Container-Lifecycle.md) | [Table of Contents](./[0]-Introduction-to-Containers.md) | [Next](./[6]-Choosing-A-Container-Tool.md)
