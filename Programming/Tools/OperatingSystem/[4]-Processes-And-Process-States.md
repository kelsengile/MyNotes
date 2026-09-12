[Previous](./[3]-System-Calls-And-The-Boot-Process.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[5]-Threads-And-Multithreading.md)

*Process Management*

# Lesson 4 - Processes And Process States

## 4.1 What Is a Process

A process is a program in execution — it includes the program's code, its current activity (represented by the program counter and CPU registers), and its own private memory space containing data, a heap, and a stack. Two runs of the very same program are two separate processes, each with its own isolated state; if one crashes or misbehaves, the other is unaffected. The OS is responsible for creating processes, allocating resources to them, scheduling their execution, and cleaning up after they terminate.

**Example:** opening two separate windows of the same web browser creates two distinct processes, each with its own memory space, open tabs, and cache. If one window crashes due to a bug, the other keeps running unaffected — a direct benefit of process isolation.

---

## 4.2 The Process Control Block

The operating system tracks every process using a data structure called the Process Control Block (PCB). The PCB stores everything the OS needs to manage and later resume the process: its process ID, current state, saved CPU register values, memory management information, open file lists, and scheduling priority. Whenever the OS switches away from a running process, it saves that process's context into its PCB; when it switches back, it restores the CPU to exactly that saved state, making it look to the process as if it never stopped running.

**Simplified PCB fields:**

```
PCB {
  process_id:        4021
  state:             Ready
  program_counter:   0x00401530
  registers:         { eax: 12, ebx: 0, ... }
  memory_info:       page table pointer
  open_files:        [stdin, stdout, config.json]
  priority:          5
}
```

---

## 4.3 Process States And Transitions

At any moment, a process is in one of a small number of states. **New** processes are being created; **Ready** processes are waiting for a turn on the CPU; **Running** processes are actively executing instructions; **Waiting** (or blocked) processes are paused until some event completes, like an I/O operation finishing; and **Terminated** processes have finished execution. Processes move between these states as events occur — a running process might get interrupted and move back to Ready, or it might request I/O and move to Waiting until that I/O completes, at which point it returns to Ready to wait for the CPU again.

```
New -> Ready -> Running -> Terminated
                  ^  |
                  |  v
                Waiting
```

**Example:** a process reads a large file. It moves from Running to Waiting the instant it issues the read request (since disk I/O takes far longer than CPU time), and only returns to Ready once the data has actually arrived from disk — during that wait, the CPU is free to run other Ready processes instead of sitting idle.

---

## 4.4 Context Switching

A context switch is the act of saving the state of a currently running process and loading the saved state of another, so the CPU can switch from executing one process to executing another. This happens constantly on any multitasking system — dozens or hundreds of times per second — giving the illusion that many programs are running simultaneously even on a machine with only a few CPU cores. Context switches aren't free: saving and restoring registers, memory mappings, and other state takes real time, so an OS scheduler tries to balance switching often enough to stay responsive against switching so often that the overhead itself slows the system down.

| Switch frequency | Responsiveness | Overhead cost |
|---|---|---|
| Too infrequent | Sluggish, laggy | Low |
| Balanced | Smooth and responsive | Moderate |
| Too frequent | Very responsive | High — CPU spends more time switching than working |

---

[Previous](./[3]-System-Calls-And-The-Boot-Process.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[5]-Threads-And-Multithreading.md)