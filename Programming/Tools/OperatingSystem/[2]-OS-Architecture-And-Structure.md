[Previous](./[1]-What-Is-An-Operating-System.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[3]-System-Calls-And-The-Boot-Process.md)

*Foundations*

# Lesson 2 - OS Architecture And Structure

## 2.1 Kernel vs User Space

Operating systems separate memory and execution into two privilege levels: kernel space and user space. The kernel is the core of the OS — it runs with full hardware privileges and manages the CPU, memory, and devices directly. User space is where ordinary applications run, with restricted privileges so a buggy or malicious program can't directly corrupt hardware or other programs' memory. When a user-space program needs a privileged operation, like reading a file, it must ask the kernel to do it on its behalf. This separation is the foundation of system stability and security.

---

## 2.2 Monolithic Kernels

In a monolithic kernel, most operating system services — process management, memory management, file systems, device drivers — run together in kernel space as a single large program. This design tends to be fast, since components can call each other directly without crossing privilege boundaries, but it also means a bug in one driver can potentially crash the entire system. Linux and traditional Unix systems are well-known examples of largely monolithic kernels, though modern versions add modularity through loadable kernel modules.

---

## 2.3 Microkernels

A microkernel takes the opposite approach: it keeps the kernel as small as possible, handling only the most essential functions like inter-process communication, basic scheduling, and low-level address space management. Services such as file systems and device drivers run as separate user-space processes instead of inside the kernel. This improves fault isolation — a crashing driver doesn't take down the whole OS — at the cost of some performance, since components communicate through message passing rather than direct function calls. Examples include QNX and the academic Minix system.

---

## 2.4 Hybrid And Modular Kernels

Most modern operating systems don't fit neatly into either extreme; they're hybrid kernels that combine ideas from both designs. Windows NT-based systems, for example, keep a relatively small core but run many subsystems close to the kernel for performance, while still isolating some services in user space. Modular kernels, like modern Linux, keep a monolithic core but allow drivers and features to be loaded and unloaded at runtime as kernel modules, gaining some of the flexibility of a microkernel without the full messaging overhead.

[Previous](./[1]-What-Is-An-Operating-System.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[3]-System-Calls-And-The-Boot-Process.md)
