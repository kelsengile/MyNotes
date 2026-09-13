[Previous](./[0]-Introduction-to-Kubernetes.md) | [Table of Contents](./[0]-Introduction-to-Kubernetes.md) | [Next](./[2]-Kubernetes-Architecture.md)

*Getting Started*

# Lesson 1 - What Is Kubernetes

## 1.1 The Problem Kubernetes Solves

Running one container on your laptop with `docker run` is straightforward. Running a production application usually means running *many* containers, across *many* servers, that need to:

- Restart automatically if they crash.
- Get rescheduled onto a healthy server if the one they're on goes down.
- Scale up when traffic increases, and back down when it doesn't.
- Receive updates without taking the whole application offline.
- Find and talk to each other reliably, even as individual containers come and go.

Doing all of this by hand, across a fleet of servers, doesn't scale. Kubernetes is a system that automates it: you describe the state you *want* (e.g. "always run 5 copies of this app"), and Kubernetes continuously works to make reality match that description.

> 💡 **Analogy:** Running containers manually is like personally checking on every food truck in a city, every hour, to make sure it's still parked, still has propane, and hasn't broken down. Kubernetes is a dispatch system that watches all of them for you and automatically sends a replacement truck the moment one goes offline.

## 1.2 Declarative Configuration

Kubernetes is built around a **declarative** model: instead of issuing step-by-step commands, you write a file describing the desired end state, and Kubernetes figures out how to get there and keep it that way.

```
   You declare:                  Kubernetes continuously:
   "I want 3 replicas             - Checks the current state
    of my-app running"     ──▶    - Starts/stops containers
                                  - Repeats forever, reacting
                                    to failures automatically
```

This is different from Docker Compose, which mostly runs the commands you give it once. Kubernetes keeps watching and correcting, indefinitely, which is the foundation of its self-healing behavior (covered in Lesson 6).

## 1.3 Key Terminology

A handful of terms come up constantly in Kubernetes and are worth learning early:

| Term | Meaning |
|---|---|
| **Cluster** | A set of machines (nodes) that Kubernetes manages together |
| **Node** | A single machine (physical or virtual) in the cluster |
| **Pod** | The smallest deployable unit — one or more tightly-coupled containers |
| **Deployment** | A description of how many copies of a Pod should be running |
| **Service** | A stable way for other things to reach a set of Pods |
| **Manifest** | A YAML file describing a desired Kubernetes resource |
| **kubectl** | The command-line tool used to interact with a cluster |

### Quick check: which term?

| Statement | Term |
|---|---|
| "The smallest thing Kubernetes schedules and runs" | Pod |
| "A single machine that's part of the cluster" | Node |
| "A YAML file describing what you want to exist" | Manifest |
| "The command-line tool you use to talk to Kubernetes" | kubectl |

With the vocabulary in place, the next lesson looks under the hood at how a Kubernetes cluster is actually structured.

---

[Previous](./[0]-Introduction-to-Kubernetes.md) | [Table of Contents](./[0]-Introduction-to-Kubernetes.md) | [Next](./[2]-Kubernetes-Architecture.md)
