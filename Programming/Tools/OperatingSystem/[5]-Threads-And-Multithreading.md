[Previous](./[4]-Processes-And-Process-States.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[6]-CPU-Scheduling.md)

*Process Management*

# Lesson 5 - Threads And Multithreading

## 5.1 What Is a Thread

A thread is the smallest unit of CPU execution within a process — a single sequential stream of instructions with its own program counter, registers, and stack. A process always has at least one thread, but it can have many, all sharing the same memory space, open files, and other process-wide resources. Because threads within a process share memory, they can communicate and cooperate much more cheaply than separate processes can, but that same sharing means a bug in one thread can more easily corrupt data used by another.

---

## 5.2 Processes vs Threads

Processes and threads both represent units of execution, but they differ in isolation and cost. Each process gets its own private address space, so processes are strongly isolated from one another and communication between them requires explicit mechanisms (see Lesson 9). Threads within the same process share memory directly, making communication trivial but removing that safety boundary. Creating and context-switching between threads is also generally much cheaper than doing so between processes, since there's no need to swap out an entire address space — this is why applications that need many concurrent, cooperating tasks (like a web browser rendering a page while downloading resources) typically use threads rather than separate processes.

---

## 5.3 User-Level vs Kernel-Level Threads

Threads can be implemented at the user level, the kernel level, or both. User-level threads are managed entirely by a library in user space, without the kernel knowing they exist; they're fast to create and switch between, but if one blocks on I/O, the whole process can block since the kernel only sees a single thread. Kernel-level threads are managed directly by the OS, which knows about each one individually and can schedule them independently across CPU cores — this adds some overhead per operation but allows true parallelism and lets one thread block without stalling its siblings. Most modern systems use kernel-level threads, sometimes layered with user-level thread libraries on top for extra flexibility.

---

## 5.4 Multithreading Models

When user-level and kernel-level threads coexist, an OS needs a strategy for mapping one to the other. In a **many-to-one** model, many user threads map to a single kernel thread, which is fast but limits parallelism. In a **one-to-one** model, every user thread gets its own kernel thread, enabling true parallel execution at the cost of higher creation overhead — this is the model most modern operating systems use by default. A **many-to-many** model maps many user threads onto a smaller or equal pool of kernel threads, trying to balance the low overhead of many-to-one with some of the parallelism of one-to-one.

[Previous](./[4]-Processes-And-Process-States.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[6]-CPU-Scheduling.md)
