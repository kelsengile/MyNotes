[Previous](./[4]-Processes-And-Process-States.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[6]-CPU-Scheduling.md)

*Process Management*

# Lesson 5 - Threads And Multithreading

## 5.1 What Is a Thread

A thread is the smallest unit of CPU execution within a process — a single sequential stream of instructions with its own program counter, registers, and stack. A process always has at least one thread, but it can have many, all sharing the same memory space, open files, and other process-wide resources. Because threads within a process share memory, they can communicate and cooperate much more cheaply than separate processes can, but that same sharing means a bug in one thread can more easily corrupt data used by another.

**Example:** a web browser tab might use one thread to render the page, another to decode a video, and a third to handle network requests — all three share the tab's memory (so the video thread can hand decoded frames directly to the rendering thread without copying them through the kernel), but a memory-corruption bug in the video decoder could, in principle, also corrupt data the rendering thread relies on.

---

## 5.2 Processes vs Threads

Processes and threads both represent units of execution, but they differ in isolation and cost. Each process gets its own private address space, so processes are strongly isolated from one another and communication between them requires explicit mechanisms (see Lesson 9). Threads within the same process share memory directly, making communication trivial but removing that safety boundary. Creating and context-switching between threads is also generally much cheaper than doing so between processes, since there's no need to swap out an entire address space — this is why applications that need many concurrent, cooperating tasks (like a web browser rendering a page while downloading resources) typically use threads rather than separate processes.

| | Process | Thread |
|---|---|---|
| Memory | Private, isolated | Shared with sibling threads |
| Creation cost | Higher | Lower |
| Context switch cost | Higher | Lower |
| Communication | Requires explicit IPC (Lesson 9) | Direct, via shared memory |
| Crash impact | Isolated to that process | Can affect the whole process |

---

## 5.3 User-Level vs Kernel-Level Threads

Threads can be implemented at the user level, the kernel level, or both. User-level threads are managed entirely by a library in user space, without the kernel knowing they exist; they're fast to create and switch between, but if one blocks on I/O, the whole process can block since the kernel only sees a single thread. Kernel-level threads are managed directly by the OS, which knows about each one individually and can schedule them independently across CPU cores — this adds some overhead per operation but allows true parallelism and lets one thread block without stalling its siblings. Most modern systems use kernel-level threads, sometimes layered with user-level thread libraries on top for extra flexibility.

**Example of the many-to-one blocking problem:** if a program has 10 user-level threads mapped onto a single kernel thread, and one of those 10 makes a blocking I/O call, the kernel only sees one thread total — so it blocks the entire kernel thread, and all 10 user-level threads freeze, even though 9 of them had nothing to do with the I/O request.

---

## 5.4 Multithreading Models

When user-level and kernel-level threads coexist, an OS needs a strategy for mapping one to the other. In a **many-to-one** model, many user threads map to a single kernel thread, which is fast but limits parallelism. In a **one-to-one** model, every user thread gets its own kernel thread, enabling true parallel execution at the cost of higher creation overhead — this is the model most modern operating systems use by default. A **many-to-many** model maps many user threads onto a smaller or equal pool of kernel threads, trying to balance the low overhead of many-to-one with some of the parallelism of one-to-one.

| Model | User threads : Kernel threads | Parallelism | Overhead |
|---|---|---|---|
| Many-to-one | Many : 1 | None | Lowest |
| One-to-one | 1 : 1 | Full | Highest |
| Many-to-many | Many : Several | Partial | Moderate |

---

[Previous](./[4]-Processes-And-Process-States.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[6]-CPU-Scheduling.md)