[Previous](./[11]-Virtual-Memory-And-Paging.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[13]-File-Systems.md)

*Memory Management*

# Lesson 12 - Segmentation

## 12.1 What Is Segmentation

Segmentation is a memory management scheme that divides a process's address space into logically meaningful, variable-sized chunks called segments — for example, separate segments for code, global data, the heap, and the stack — rather than the fixed-size, arbitrary pages used in paging. Each segment is referenced by a segment number and an offset within it, and a segment table maps each segment to its location and size in physical memory. Because segmentation mirrors how programmers already think about a program's structure, it can make protection and sharing more natural to express.

**Example segment table for a process:**

| Segment | Base address | Length |
|---|---|---|
| Code | 0x10000 | 4KB |
| Data | 0x20000 | 8KB |
| Heap | 0x30000 | 16KB (grows) |
| Stack | 0x80000 | 8KB (grows) |

---

## 12.2 Segmentation vs Paging

Paging and segmentation solve similar problems from different angles. Paging divides memory into fixed-size, physically arbitrary chunks, which eliminates external fragmentation but has no relationship to the logical structure of a program. Segmentation divides memory into variable-sized, logically meaningful chunks, which fits program structure naturally but reintroduces external fragmentation, since segments of different sizes must still fit into contiguous physical space. Neither approach is strictly better — they represent a trade-off between physical memory efficiency and logical clarity.

| | Paging | Segmentation |
|---|---|---|
| Chunk size | Fixed | Variable |
| Matches program structure | No | Yes |
| External fragmentation | None | Possible |
| Internal fragmentation | Small amount (last page) | None |

---

## 12.3 Segmented Paging

Many real systems combine the two ideas in a scheme called segmented paging (or paged segmentation): a process's address space is first divided into logical segments, and each segment is then further divided into fixed-size pages. This gets the logical organization and protection benefits of segmentation while still eliminating external fragmentation through paging within each segment. The trade-off is added complexity in address translation, since accessing memory now requires resolving both a segment table and a page table.

```
Virtual address -> [Segment number | Page number | Offset]
                          |               |            |
                   Segment Table    Page Table    Byte within page
                          |               |
                          v               v
                    Base of segment's page table -> Physical frame
```

---

## 12.4 Protection And Sharing

Because segments correspond to logical units of a program, they make it natural to apply different protection rules to different parts of memory — a code segment can be marked read-only and executable, while a data segment is marked read-write but not executable, catching whole classes of bugs and security issues at the hardware level. Segmentation also makes sharing more intuitive: two processes running the same program can share a single read-only code segment while keeping their own private data segments, saving physical memory without any risk of one process corrupting another's code.

| Segment | Typical permissions |
|---|---|
| Code | Read + Execute (no write) |
| Data | Read + Write (no execute) |
| Stack | Read + Write (no execute) |

Marking the code segment non-writable and the stack non-executable is also a real-world security defense — it's part of what makes certain classic buffer-overflow attacks (Lesson 16) harder to pull off.

---

[Previous](./[11]-Virtual-Memory-And-Paging.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[13]-File-Systems.md)