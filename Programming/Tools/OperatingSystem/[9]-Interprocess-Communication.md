[Previous](./[8]-Deadlocks.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[10]-Memory-Management-Basics.md)

*Concurrency And Synchronization*

# Lesson 9 - Interprocess Communication

## 9.1 Shared Memory

Since separate processes normally have isolated address spaces, they need an explicit mechanism to communicate — this is called interprocess communication (IPC). One approach is shared memory, where the OS maps a region of physical memory into the address spaces of two or more processes, letting them read and write it as if it were ordinary local memory. This is extremely fast, since no data has to be copied through the kernel, but it pushes the responsibility for synchronizing access (using the tools from Lesson 7) entirely onto the processes themselves.

| | Shared memory | Message passing |
|---|---|---|
| Speed | Very fast (no kernel copying) | Slower (data copied through kernel) |
| Synchronization | Manual (locks, semaphores) | Handled implicitly by message delivery |
| Works across a network | No | Yes |

---

## 9.2 Message Passing

Message passing takes the opposite approach: processes exchange data by sending and receiving discrete messages through the kernel, rather than sharing memory directly. This is generally slower than shared memory, since each message must be copied, but it's simpler to reason about and works naturally even between processes on different machines connected over a network. Message passing can be **direct**, where processes name each other explicitly, or **indirect**, where messages are sent to and received from a shared mailbox or port.

```
Direct:    ProcessA.send(ProcessB, "task complete")
Indirect:  ProcessA.send(mailbox_42, "task complete")
           ProcessB.receive(mailbox_42)
```

---

## 9.3 Pipes And Sockets

Pipes and sockets are common OS-provided mechanisms built on message passing. A pipe is a unidirectional communication channel, typically used to connect the output of one process directly to the input of another (the `|` operator in a command-line shell is a familiar example). Named pipes extend this idea to processes that aren't directly related. Sockets provide a more general, bidirectional communication endpoint that can connect processes on the same machine or across a network, forming the foundation for most client-server and networked applications.

**Example:** the shell command

```bash
cat access.log | grep "ERROR" | wc -l
```

connects three processes through two pipes: `cat`'s output feeds directly into `grep`'s input, and `grep`'s output feeds into `wc`'s input — no temporary files are created, and each process starts consuming data as soon as the previous one produces it.

---

## 9.4 Signals

Signals are a lightweight IPC mechanism used to notify a process that a specific event has occurred, without transferring any substantial data. A signal might indicate that a user pressed Ctrl+C, that a process tried to access invalid memory, or that a timer has expired. When a process receives a signal, it can handle it with a custom handler function, ignore it, or let the OS perform a default action, such as terminating the process. Because signals interrupt a process's normal flow of execution, they're best suited for simple notifications rather than transferring complex data between processes.

| Signal (Unix example) | Meaning |
|---|---|
| `SIGINT` | User pressed Ctrl+C |
| `SIGSEGV` | Invalid memory access |
| `SIGALRM` | A timer expired |
| `SIGKILL` | Forcibly terminate the process (cannot be caught or ignored) |

---

[Previous](./[8]-Deadlocks.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[10]-Memory-Management-Basics.md)