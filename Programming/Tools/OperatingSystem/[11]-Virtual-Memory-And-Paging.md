[Previous](./[10]-Memory-Management-Basics.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[12]-Segmentation.md)

*Memory Management*

# Lesson 11 - Virtual Memory And Paging

## 11.1 What Is Virtual Memory

Virtual memory is a technique that gives each process the illusion of a large, private, contiguous address space, regardless of how much physical RAM is actually installed or how fragmented it is. The OS and CPU work together to translate virtual addresses used by a program into physical addresses in RAM, and only the portions of a process actually in active use need to be resident in physical memory at any given moment. This allows a system to run programs larger than physical memory, isolate processes from each other, and manage memory far more flexibly than contiguous allocation alone would allow.

**Example:** a program might have a virtual address space of 4GB, even on a machine with only 8GB of physical RAM shared across dozens of running programs — the OS only keeps the small portion of that 4GB actually being touched resident in RAM at any moment.

---

## 11.2 Paging And Page Tables

Paging implements virtual memory by dividing both virtual and physical memory into fixed-size blocks: virtual memory into "pages" and physical memory into "frames" of the same size. Each process has a page table that maps its virtual pages to physical frames; a page doesn't need a contiguous run of physical memory, since each of its pages can live in any free frame anywhere in RAM. This eliminates external fragmentation entirely, since any free frame can satisfy any page request, though a small amount of internal fragmentation remains in the last, partially-used page of a process.

**Example page table lookup:**

```
Virtual Page 0 -> Physical Frame 7
Virtual Page 1 -> Physical Frame 2
Virtual Page 2 -> Physical Frame 19
```

Page 0 and Page 1 are logically adjacent in the process's address space, but Frame 7 and Frame 2 might be anywhere in physical RAM — the process never notices, since the page table hides the scattering.

---

## 11.3 Page Replacement Algorithms

Because physical memory is limited, not every page a process wants can stay resident in RAM at once — the OS must sometimes evict a page to make room for another, a decision made by a page replacement algorithm. **FIFO** evicts the oldest-loaded page, which is simple but can perform poorly. **Least Recently Used (LRU)** evicts the page that hasn't been accessed for the longest time, on the theory that pages used recently are likely to be used again soon; it performs well in practice but is expensive to track exactly, so most real systems use efficient approximations of it.

**Example:** with only 3 frames available and pages requested in order `1, 2, 3, 4, 1, 2`:

| Algorithm | Page faults |
|---|---|
| FIFO | Evicts page 1 for page 4, then must reload 1 and 2 again — more faults |
| LRU | Evicts based on actual recent use, typically fewer faults on this pattern | The goal of any replacement algorithm is to minimize page faults — the events where a program accesses a page that isn't currently in RAM and must be loaded from disk.

---

## 11.4 Thrashing

Thrashing occurs when a system is spending more time paging data in and out of memory than actually executing useful work, usually because too many processes are competing for too little physical RAM. Each process needs pages loaded to make progress, but loading them evicts pages another process still needs, which then triggers more page faults, in a vicious cycle that can bring overall system throughput to a crawl even though the CPU appears busy. Solutions include reducing the number of concurrently running processes, giving each process a working set of memory sized to its actual needs, or simply adding more physical RAM.

> **Symptom to recognize:** a computer that feels frozen, has a disk activity light constantly flashing, yet shows low CPU usage in a task manager is a classic sign of thrashing — the CPU is idle not because there's no work, but because it's waiting on a flood of page faults being resolved from disk.

---

[Previous](./[10]-Memory-Management-Basics.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[12]-Segmentation.md)