 [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[2]-OS-Architecture-And-Structure.md)

*Foundations*

# Lesson 1 - What Is an Operating System

## 1.1 Defining an Operating System

An operating system is the software that manages a computer's hardware and provides a common set of services to the programs running on it. Without an OS, every application would need to know how to talk directly to the CPU, memory chips, disk controller, and every other piece of hardware — an unworkable amount of duplicated, fragile work. Instead, the OS sits between hardware and application software, exposing clean, consistent interfaces (like "open a file" or "allocate memory") that hide the messy hardware details underneath. Examples include Windows, macOS, Linux, Android, and iOS.

| Without an OS | With an OS |
|---|---|
| App must know the exact disk controller model to save a file | App calls a generic "write file" function |
| App must manage physical RAM addresses directly | App gets a private virtual address space |
| Only one program can realistically run at a time | Many programs run concurrently, managed automatically |

---

## 1.2 The OS as Resource Manager

A computer has a limited set of resources — CPU time, memory, disk space, network bandwidth, and peripheral devices — that must be shared among many competing programs. The OS acts as a resource manager, deciding which process gets the CPU next, how memory is divided up, and in what order I/O requests are serviced. Good resource management keeps the system fair, efficient, and responsive, preventing any single program from starving the others or crashing the machine.

**Example:** on a laptop with 8 CPU cores and 50 running processes, the OS doesn't dedicate one core per process — it rapidly switches processes on and off each core dozens of times per second, giving the appearance that all 50 are running simultaneously even though only 8 can truly execute at any single instant.

---

## 1.3 The OS as Abstraction Layer

Beyond managing resources, the OS also abstracts hardware complexity away from developers. A programmer writing code to save a file doesn't need to know whether the underlying storage is a solid-state drive, a spinning hard disk, or a network share — the OS exposes a uniform file interface regardless of the physical medium. This abstraction extends to memory (programs see a private address space rather than raw physical RAM), devices (a common driver interface rather than device-specific wiring), and processes (each program appears to have the CPU to itself). Abstraction is what makes software portable and developers productive.

| Real hardware detail | What the OS exposes instead |
|---|---|
| SSD, HDD, or network share | A uniform file (`open`, `read`, `write`, `close`) |
| Physical RAM shared across all processes | A private virtual address space per process |
| Dozens of different printer/network chipsets | A common driver interface |

---

## 1.4 A Brief History of Operating Systems

Early computers in the 1940s and 50s had no operating system at all — operators fed in programs one at a time via punch cards, and each program controlled the entire machine. Batch processing systems emerged in the late 1950s to queue up jobs automatically, followed by time-sharing systems in the 1960s that let multiple users interact with a computer at once by rapidly switching between them. The 1970s and 80s brought personal computing (with systems like MS-DOS and early Unix), and the graphical, networked, and eventually mobile and cloud-based operating systems of today evolved from those foundations. Understanding this history helps explain why modern OS concepts — like multitasking and protection — exist: they were solutions to real problems as computing scaled up.

| Era | Development | Problem it solved |
|---|---|---|
| 1940s-50s | No OS, punch cards | N/A — earliest computers |
| Late 1950s | Batch processing | Reduced idle time between jobs |
| 1960s | Time-sharing | Let multiple users share one expensive computer |
| 1970s-80s | Personal computing (MS-DOS, Unix) | Made computing affordable and individual |
| Today | Mobile, cloud, graphical OSes | Portability, scale, and ease of use |

---
 [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[2]-OS-Architecture-And-Structure.md)