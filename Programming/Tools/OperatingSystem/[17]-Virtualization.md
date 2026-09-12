[Previous](./[16]-Protection-And-Security.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[18]-Choosing-And-Exploring-Operating-Systems.md)

*Security And Advanced Topics*

# Lesson 17 - Virtualization

## 17.1 What Is Virtualization

Virtualization is the technique of creating a software-based, simulated version of some resource — commonly an entire computer — so that it can run its own independent operating system as if it had dedicated hardware, even though it's actually sharing physical hardware with other virtual machines. This lets a single physical server run many isolated environments at once, each unaware of the others, which is foundational to how modern data centers and cloud platforms achieve efficient hardware utilization, isolation between customers, and flexible resource allocation.

---

## 17.2 Hypervisors

The software that creates and manages virtual machines is called a hypervisor. A **Type 1 (bare-metal) hypervisor** runs directly on the physical hardware, without a host operating system underneath it, and is typically used in servers and data centers for its performance and strong isolation. A **Type 2 (hosted) hypervisor** runs as an application on top of a conventional host operating system, which is more convenient for running virtual machines on a personal desktop or laptop but incurs more overhead. In both cases, the hypervisor is responsible for presenting each virtual machine with what looks like its own dedicated CPU, memory, and devices, while actually multiplexing the real hardware underneath.

---

## 17.3 Containers vs Virtual Machines

Containers offer a lighter-weight alternative to full virtual machines. Rather than virtualizing an entire computer and running a separate guest OS kernel per instance, containers share the host machine's kernel while isolating each container's processes, file system view, and resources using OS-level features (like namespaces and control groups on Linux). This makes containers much faster to start and far less resource-hungry than full VMs, at the cost of weaker isolation, since all containers on a host ultimately share the same underlying kernel. Virtual machines remain preferable when strong isolation or running a different OS entirely is required; containers are typically preferred for efficiently packaging and deploying many instances of an application.

---

## 17.4 Cloud Computing And The OS

Cloud computing platforms are built heavily on the virtualization concepts in this lesson, letting customers rent virtual machines or containers on demand rather than owning physical hardware. This shifts many traditional OS-level concerns — provisioning resources, isolating tenants, scaling capacity up and down — to a much larger scale, managed by the cloud provider's own layer of orchestration software sitting on top of hypervisors and container runtimes. Understanding the fundamentals of processes, memory, and isolation covered throughout this Topic is exactly what makes those higher-level cloud and container concepts make sense, since they're built from the same underlying OS building blocks, just composed and automated at scale.

[Previous](./[16]-Protection-And-Security.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[18]-Choosing-And-Exploring-Operating-Systems.md)
