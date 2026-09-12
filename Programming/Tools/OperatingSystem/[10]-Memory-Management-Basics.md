[Previous](./[9]-Interprocess-Communication.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[11]-Virtual-Memory-And-Paging.md)

*Memory Management*

# Lesson 10 - Memory Management Basics

## 10.1 Address Spaces

Every process has its own address space — the range of memory addresses it can use, holding its code, data, heap, and stack. Modern operating systems give each process a private virtual address space that looks like it has the entire machine's memory to itself, even though physical RAM is actually shared among many processes. The OS, with hardware support, maps each process's virtual addresses to real physical memory locations behind the scenes, so processes never see or interfere with each other's memory directly.

**Example:** two separate processes can both use virtual address `0x00400000` for their code — the OS maps each one to a completely different physical RAM location behind the scenes, so despite the identical-looking address, they never actually collide.

---

## 10.2 Contiguous Memory Allocation

In early and simple memory management schemes, each process is allocated a single contiguous block of physical memory large enough to hold it entirely. The OS keeps track of which regions are free and which are allocated, choosing a free block for each new process using strategies like first-fit (the first block big enough), best-fit (the smallest block that still fits), or worst-fit (the largest available block). Contiguous allocation is simple to implement and reason about, but it struggles as processes are created and destroyed over time, leaving memory in an increasingly patchy state.

| Strategy | Rule | Trade-off |
|---|---|---|
| First-fit | Use the first free block big enough | Fast, but can leave awkward leftover gaps |
| Best-fit | Use the smallest block that still fits | Minimizes leftover space, but slower to search |
| Worst-fit | Use the largest available block | Leaves large usable remainders, but wastes big blocks fast |

---

## 10.3 Fragmentation

Fragmentation is wasted memory that accumulates as a side effect of allocation and deallocation. **External fragmentation** happens when there's enough total free memory to satisfy a request, but it's scattered in small, non-contiguous pieces rather than one usable block. **Internal fragmentation** happens when memory is allocated in fixed-size chunks and a process doesn't use the entire chunk it was given, wasting the leftover space inside its own allocation. Techniques like paging (Lesson 11) and segmentation (Lesson 12) were developed largely to combat these forms of waste.

**Example external fragmentation:** free memory totals 500KB, but it's scattered as three 150KB, 200KB, and 150KB gaps between allocated blocks — a request for a single 400KB block fails even though 500KB is technically free, because no single gap is large enough.

**Example internal fragmentation:** memory is allocated in fixed 64KB chunks, and a process only needs 50KB — the remaining 14KB inside its own chunk is wasted and unusable by anything else.

---

## 10.4 Swapping

Swapping is the technique of temporarily moving an entire process (or parts of it) out of main memory and onto disk when physical memory is scarce, then bringing it back in later when it's needed and memory is available again. This lets a system support more processes, or larger processes, than would fit in RAM at once, but disk access is dramatically slower than RAM, so heavy swapping can severely degrade performance — a symptom often called "thrashing," which is explored further in Lesson 11. Modern systems combine swapping with virtual memory so that only the specific portions of a process actually being used need to be resident in RAM at any given time.

| Scenario | Effect |
|---|---|
| Plenty of free RAM | No swapping needed, full speed |
| RAM nearly full | Some inactive process data swapped to disk |
| Severe RAM shortage | Heavy, constant swapping — "thrashing" (Lesson 11) |

---

[Previous](./[9]-Interprocess-Communication.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[11]-Virtual-Memory-And-Paging.md)