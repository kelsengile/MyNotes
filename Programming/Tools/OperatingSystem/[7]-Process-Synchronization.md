[Previous](./[6]-CPU-Scheduling.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[8]-Deadlocks.md)

*Concurrency And Synchronization*

# Lesson 7 - Process Synchronization

## 7.1 The Critical Section Problem

When multiple processes or threads share data, problems arise if more than one tries to read and modify that data at the same time — the result can depend on the unpredictable timing of each one's execution, a bug known as a race condition. A **critical section** is the part of a program's code that accesses shared data and must not be executed by more than one thread at a time. The critical section problem is the challenge of designing a protocol that guarantees mutual exclusion (only one thread in the critical section at once), progress (waiting threads aren't blocked forever by threads outside the section), and bounded waiting (no thread waits indefinitely while others repeatedly cut in line).

**Example race condition:** two threads both run `balance = balance + 100` on a shared bank balance starting at 500. If both read `balance` (500) before either writes back, both compute 600 and write it — the final balance is 600 instead of the correct 700, because one deposit was silently lost.

---

## 7.2 Locks And Mutexes

The simplest tool for solving the critical section problem is a lock (or mutex, short for "mutual exclusion"). A thread must acquire the lock before entering a critical section and release it afterward; if the lock is already held, any other thread trying to acquire it must wait. This guarantees only one thread is ever inside the protected section at a time. Locks are simple and widely used, but they come with risks: forgetting to release a lock can freeze other threads forever, and using multiple locks carelessly can lead to deadlock (covered in Lesson 8).

```
lock.acquire()
balance = balance + 100   // critical section
lock.release()
```

With the lock in place, the race condition from 7.1 can't happen — the second thread simply waits at `lock.acquire()` until the first thread finishes and releases it.

---

## 7.3 Semaphores

A semaphore is a more general synchronization tool: an integer variable, accessed only through two atomic operations, traditionally called `wait` (or P) and `signal` (or V). A **binary semaphore**, restricted to values 0 and 1, works much like a mutex. A **counting semaphore** can take a wider range of values and is useful for managing a pool of identical resources — for example, a semaphore initialized to the number of available database connections, where each thread waits on the semaphore before taking a connection and signals it when done. Semaphores are powerful but easy to misuse, since a missing or misplaced wait/signal call can silently break the intended guarantees.

**Example:** a connection pool with 5 database connections uses a counting semaphore initialized to 5:

```
semaphore.wait()      // decrements; blocks if already at 0
connection = pool.take()
... use connection ...
pool.return(connection)
semaphore.signal()    // increments, waking a waiting thread if any
```

A 6th thread requesting a connection simply blocks at `wait()` until one of the first 5 finishes and calls `signal()`.

---

## 7.4 Monitors

Monitors offer a higher-level, less error-prone alternative to raw locks and semaphores. A monitor bundles shared data together with the procedures that operate on it, and the language or runtime automatically ensures that only one thread can execute inside the monitor's procedures at a time — the programmer doesn't have to remember to acquire and release a lock manually. Monitors also typically provide condition variables, which let a thread inside the monitor wait for a specific condition to become true and be woken up once another thread signals that it has. Many modern languages provide monitor-like constructs (such as `synchronized` methods) built directly into the language, making correct synchronization much easier to get right than with semaphores alone.

| Tool | Who manages locking | Risk of forgetting to unlock |
|---|---|---|
| Lock/Mutex | The programmer, manually | High |
| Semaphore | The programmer, manually | High |
| Monitor | The language/runtime, automatically | Low |

---

[Previous](./[6]-CPU-Scheduling.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[8]-Deadlocks.md)