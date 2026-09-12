[Previous](./[7]-Process-Synchronization.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[9]-Interprocess-Communication.md)

*Concurrency And Synchronization*

# Lesson 8 - Deadlocks

## 8.1 Conditions For Deadlock

A deadlock occurs when a group of processes are each waiting for a resource held by another process in the group, so none of them can ever proceed. Four conditions must all hold simultaneously for a deadlock to be possible: **mutual exclusion** (resources can't be shared, only held by one process at a time), **hold and wait** (a process holding a resource can request more while continuing to hold what it already has), **no preemption** (a resource can't be forcibly taken from a process, only released voluntarily), and **circular wait** (a cycle of processes exists, each waiting for a resource held by the next). Understanding these conditions is the key to preventing, avoiding, or recovering from deadlocks.

**Classic example — two processes, two resources:**

```
Process A holds Printer,  wants Scanner
Process B holds Scanner,  wants Printer
```

Neither can proceed: A waits forever for the Scanner that B holds, and B waits forever for the Printer that A holds — a circular wait, satisfying all four deadlock conditions at once.

---

## 8.2 Deadlock Prevention

Deadlock prevention works by structurally ensuring that at least one of the four necessary conditions can never occur. For example, requiring processes to request all the resources they'll ever need up front eliminates hold-and-wait, and imposing a strict global ordering on resource acquisition (so every process must request resources in the same relative order) eliminates circular wait. These techniques guarantee deadlocks can't happen, but often at the cost of efficiency — requesting everything up front can waste resources that sit idle, and rigid orderings can be inconvenient to program around.

| Condition targeted | Prevention technique | Downside |
|---|---|---|
| Hold and wait | Request all resources up front | Resources may sit idle unused |
| Circular wait | Impose a global resource-ordering | Inconvenient, rigid programming |
| No preemption | Allow resources to be forcibly reclaimed | Can lose partial work |

---

## 8.3 Deadlock Avoidance

Rather than ruling out deadlock conditions entirely, deadlock avoidance lets the OS grant resource requests dynamically, but only if doing so keeps the system in a "safe state" — one where there's still some order in which all processes could finish, even in the worst case. The classic example is the Banker's Algorithm, which simulates hypothetical resource allocations before granting a real request, and denies (or delays) any request that could lead to an unsafe state. Avoidance requires processes to declare their maximum possible resource needs in advance, which isn't always practical, but it allows more flexibility than strict prevention.

**Example safe vs unsafe state:** with 10 total tape drives, if Process A (max need 9, currently holds 3) requests one more drive while 4 remain free, granting it (leaving 3 free) is safe only if some ordering still lets every process eventually finish — the Banker's Algorithm checks this by simulation before approving the request, rather than just checking if enough drives are free right now.

---

## 8.4 Deadlock Detection And Recovery

Some systems don't try to prevent or avoid deadlock at all — instead, they let it happen and periodically check for it using detection algorithms that look for cycles in a resource-allocation graph. Once a deadlock is detected, the OS must recover, typically by terminating one or more of the deadlocked processes (releasing their resources and breaking the cycle) or by preempting resources from some processes and rolling them back. This approach trades some risk and recovery cost for simplicity and better resource utilization in the common case where deadlocks are rare.

| Strategy | When it acts | Cost |
|---|---|---|
| Prevention | Before deadlock can ever occur | Ongoing efficiency loss |
| Avoidance | At each resource request | Requires advance knowledge of max needs |
| Detection & recovery | After deadlock has occurred | Risk of lost work during recovery |

---

[Previous](./[7]-Process-Synchronization.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[9]-Interprocess-Communication.md)