[Previous](./[6]-Cloud-Networking-Basics.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[8]-Containers-and-Orchestration-Basics.md)

*Compute*

# Lesson 7 - Virtual Machines & Instances

## 7.1 What Is a Virtual Machine?

A **virtual machine (VM)**, called an "instance" in most cloud consoles, is a software-emulated computer running on top of physical hardware shared among many customers via a hypervisor. Each VM has its own operating system, CPU/memory allocation, and storage, and behaves like a dedicated physical machine from the user's perspective. You choose a base **image** (also called an AMI on AWS) containing a pre-installed OS — Linux distributions and Windows Server are the most common — and the provider boots a fresh instance from it in seconds to minutes.

---

## 7.2 Instance Types and Sizing

Providers offer instance types grouped into families optimized for different workloads:

- **General purpose** — balanced CPU/memory, good default choice (e.g. AWS `t3`, `m5`).
- **Compute optimized** — high CPU-to-memory ratio, for CPU-bound workloads like batch processing (e.g. AWS `c5`).
- **Memory optimized** — for in-memory databases and caches (e.g. AWS `r5`).
- **Storage optimized** — high-speed local disk for data-intensive workloads.
- **GPU instances** — for machine learning training/inference and rendering.

Each type comes in multiple sizes (e.g. `t3.micro`, `t3.large`) representing different vCPU/RAM allocations, priced accordingly. Choosing the right type/size is a balance between performance and cost — oversized instances waste money, undersized ones bottleneck your application.

---

## 7.3 Managing VM Lifecycle

VM instances move through states you control: **launch** (create from an image), **stop** (shut down but retain disk/config, no compute charges), **start** (resume a stopped instance), **reboot**, and **terminate** (permanently delete the instance and, depending on settings, its attached storage). Best practices for managing VMs include:

- Using startup scripts (user data) to automate initial configuration when an instance boots.
- Attaching persistent storage volumes separately from the instance so data survives termination.
- Tagging instances (see Lesson 42) to track ownership, environment, and cost.
- Stopping (not just leaving idle) instances you aren't using, since running compute is billed by the hour/second regardless of load.

---

[Previous](./[6]-Cloud-Networking-Basics.md) | [Table of Contents](./%5B0%5D-Introduction.-to-Cloud-Development.md) | [Next](./[8]-Containers-and-Orchestration-Basics.md)
