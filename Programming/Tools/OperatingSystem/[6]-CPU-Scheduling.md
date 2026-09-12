[Previous](./[5]-Threads-And-Multithreading.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[7]-Process-Synchronization.md)

*Process Management*

# Lesson 6 - CPU Scheduling

## 6.1 Scheduling Goals And Criteria

Because there are usually far more ready processes than CPU cores, the OS scheduler must decide which process runs next. Different systems prioritize different goals: **throughput** (maximizing the number of processes completed per unit time), **turnaround time** (minimizing how long a process takes from submission to completion), **waiting time** (minimizing time spent in the ready queue), **response time** (minimizing delay before a process starts producing output), and **fairness** (giving every process a reasonable share of the CPU). These goals often conflict — optimizing for throughput can hurt response time — so real schedulers make deliberate trade-offs based on what kind of system they're built for.

---

## 6.2 Common Scheduling Algorithms

Several classic algorithms illustrate the trade-offs schedulers face. **First-Come, First-Served (FCFS)** runs processes in arrival order — simple, but a long process can make everyone else wait. **Shortest Job First (SJF)** runs the process with the smallest estimated CPU burst next, minimizing average waiting time but requiring an estimate of how long a process will run. **Priority Scheduling** runs the highest-priority process first, which can starve low-priority processes without safeguards like priority aging. **Round Robin** gives each process a small fixed time slice before moving to the next, cycling through the ready queue — this keeps response times low and is the basis for most interactive, time-sharing systems.

---

## 6.3 Preemptive vs Non-Preemptive Scheduling

A scheduling algorithm is non-preemptive if, once a process starts running, it keeps the CPU until it voluntarily gives it up (by finishing or blocking on I/O). It's preemptive if the OS can forcibly interrupt a running process — for example, when its time slice expires or a higher-priority process becomes ready — and give the CPU to someone else. Preemptive scheduling is essential for interactive and real-time systems, since it guarantees no single process can monopolize the CPU indefinitely, but it adds complexity: the OS must safely save and restore state at unpredictable points and guard shared data against being read mid-update.

---

## 6.4 Multilevel Queue Scheduling

Real-world workloads aren't all alike — interactive tasks need fast response times, while background batch jobs care more about throughput. Multilevel queue scheduling addresses this by splitting the ready queue into several separate queues, each with its own scheduling algorithm and priority, such as a foreground queue using Round Robin for interactive processes and a background queue using FCFS for batch jobs. A related refinement, the multilevel feedback queue, allows processes to move between queues over time based on their observed behavior — a process that uses a lot of CPU time without blocking might be demoted to a lower-priority queue, while one that blocks frequently for I/O might be promoted, letting the scheduler adapt without needing to know a process's characteristics in advance.

[Previous](./[5]-Threads-And-Multithreading.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[7]-Process-Synchronization.md)
