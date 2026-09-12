[Previous](./[14]-IO-Systems-And-Device-Management.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[16]-Protection-And-Security.md)

*Storage And I/O*

# Lesson 15 - Disk Scheduling

## 15.1 Disk Structure Basics

Traditional spinning hard disks store data on circular platters divided into tracks, and reading or writing data requires physically moving a read/write head to the correct track before the desired sector spins underneath it. This mechanical movement, called seek time, is by far the slowest part of a disk access — much slower than the actual data transfer once the head is in position. Solid-state drives have no moving parts and therefore no seek time in the same sense, but many of the same scheduling ideas still matter for maximizing throughput and managing wear, and understanding the mechanical case makes the underlying trade-offs clearer.

| Storage type | Seek time | Scheduling still matters? |
|---|---|---|
| Spinning hard disk | High (mechanical movement) | Yes, heavily |
| Solid-state drive | None | Yes, but for throughput/wear rather than seek |

---

## 15.2 Disk Scheduling Algorithms

Because seek time dominates disk performance, the order in which pending I/O requests are serviced matters a great deal. **FCFS** services requests in the order they arrive, which is fair but can cause the head to move back and forth inefficiently across the disk. **SSTF (Shortest Seek Time First)** always services whichever pending request is physically closest to the head's current position, reducing total seek movement but risking starvation of requests far from the current activity. **SCAN** (and its variant C-SCAN) moves the head steadily in one direction, servicing every request along the way, then reverses (or jumps back to the start) once it reaches the end — like an elevator, this bounds the worst-case wait time while still keeping seek distances low.

**Example:** pending requests for tracks 55, 14, 90, 40 with the head currently at track 50:

| Algorithm | Service order | Total head movement |
|---|---|---|
| FCFS | 55, 14, 90, 40 | 5 + 41 + 76 + 50 = 172 |
| SSTF | 55, 40, 14, 90 | 5 + 15 + 26 + 76 = 122 |
| SCAN (moving toward higher tracks) | 55, 90, 40, 14 | 5 + 35 + 50 + 26 = 116 |

---

## 15.3 RAID

RAID (Redundant Array of Independent Disks) combines multiple physical disks into a single logical unit to improve performance, reliability, or both. **RAID 0** stripes data across disks for higher throughput but offers no redundancy — losing one disk loses all the data. **RAID 1** mirrors data identically across two or more disks, tolerating a disk failure at the cost of doubling storage requirements. **RAID 5** stripes data along with distributed parity information across three or more disks, allowing the array to survive a single disk failure while using storage more efficiently than mirroring. Different RAID levels represent different points on the trade-off between capacity, performance, and fault tolerance.

| RAID level | Redundancy | Usable capacity (from N disks) | Survives disk failure? |
|---|---|---|---|
| RAID 0 | None | 100% | No |
| RAID 1 | Full mirror | 50% | Yes (1 disk) |
| RAID 5 | Distributed parity | ~(N-1)/N | Yes (1 disk) |

---

## 15.4 The Storage Hierarchy

Computer storage exists in a hierarchy that trades capacity for speed: CPU registers are the fastest and smallest, followed by cache memory, then main memory (RAM), then solid-state or spinning disk storage, and finally slower archival or network storage at the bottom. Each level acts as a faster, smaller cache for the level below it — frequently used disk data is cached in RAM, frequently used RAM data is cached in the CPU cache, and so on. Operating systems are deeply involved in managing several of these layers, especially the boundary between RAM and disk through virtual memory (Lesson 11) and disk caching, working to keep the most relevant data as close to the CPU as possible.

```
CPU Registers      <- fastest, smallest, most expensive per byte
CPU Cache (L1-L3)
Main Memory (RAM)
Solid-State / Hard Disk
Network / Archival Storage   <- slowest, largest, cheapest per byte
```

---

[Previous](./[14]-IO-Systems-And-Device-Management.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[16]-Protection-And-Security.md)