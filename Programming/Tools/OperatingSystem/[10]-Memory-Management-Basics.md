[Previous](./[9]-Interprocess-Communication.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[11]-Virtual-Memory-And-Paging.md)

*Memory Management*

# Lesson 10 - Memory Management Basics

## 10.1 Address Spaces

Every process has its own address space — the range of memory addresses it can use, holding its code, data, heap, and stack. Modern operating systems give each process a private virtual address space that looks like it has the entire machine's memory to itself, even though physical RAM is actually shared among many processes. The OS, with hardware support, maps each process's virtual addresses to real physical memory locations behind the scenes, so processes never see or interfere with each other's memory directly.

---

## 10.2 Contiguous Memory Allocation

In early and simple memory management schemes, each process is allocated a single contiguous block of physical memory large enough to hold it entirely. The OS keeps track of which regions are free and which are allocated, choosing a free block for each new process using strategies like first-fit (the first block big enough), best-fit (the smallest block that still fits), or worst-fit (the largest available block). Contiguous allocation is simple to implement and reason about, but it struggles as processes are created and destroyed over time, leaving memory in an increasingly patchy state.

---

## 10.3 Fragmentation

Fragmentation is wasted memory that accumulates as a side effect of allocation and deallocation. **External fragmentation** happens when there's enough total free memory to satisfy a request, but it's scattered in small, non-contiguous pieces rather than one usable block. **Internal fragmentation** happens when memory is allocated in fixed-size chunks and a process doesn't use the entire chunk it was given, wasting the leftover space inside its own allocation. Techniques like paging (Lesson 11) and segmentation (Lesson 12) were developed largely to combat these forms of waste.

---

## 10.4 Swapping

Swapping is the technique of temporarily moving an entire process (or parts of it) out of main memory and onto disk when physical memory is scarce, then bringing it back in later when it's needed and memory is available again. This lets a system support more processes, or larger processes, than would fit in RAM at once, but disk access is dramatically slower than RAM, so heavy swapping can severely degrade performance — a symptom often called "thrashing," which is explored further in Lesson 11. Modern systems combine swapping with virtual memory so that only the specific portions of a process actually being used need to be resident in RAM at any given time.

[Previous](./[9]-Interprocess-Communication.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[11]-Virtual-Memory-And-Paging.md)
