[Previous](./[14]-IO-Systems-And-Device-Management.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[16]-Protection-And-Security.md)

*Storage And I/O*

# Lesson 15 - Disk Scheduling

## 15.1 Disk Structure Basics

Traditional spinning hard disks store data on circular platters divided into tracks, and reading or writing data requires physically moving a read/write head to the correct track before the desired sector spins underneath it. This mechanical movement, called seek time, is by far the slowest part of a disk access — much slower than the actual data transfer once the head is in position. Solid-state drives have no moving parts and therefore no seek time in the same sense, but many of the same scheduling ideas still matter for maximizing throughput and managing wear, and understanding the mechanical case makes the underlying trade-offs clearer.

---

## 15.2 Disk Scheduling Algorithms

Because seek time dominates disk performance, the order in which pending I/O requests are serviced matters a great deal. **FCFS** services requests in the order they arrive, which is fair but can cause the head to move back and forth inefficiently across the disk. **SSTF (Shortest Seek Time First)** always services whichever pending request is physically closest to the head's current position, reducing total seek movement but risking starvation of requests far from the current activity. **SCAN** (and its variant C-SCAN) moves the head steadily in one direction, servicing every request along the way, then reverses (or jumps back to the start) once it reaches the end — like an elevator, this bounds the worst-case wait time while still keeping seek distances low.

---

## 15.3 RAID

RAID (Redundant Array of Independent Disks) combines multiple physical disks into a single logical unit to improve performance, reliability, or both. **RAID 0** stripes data across disks for higher throughput but offers no redundancy — losing one disk loses all the data. **RAID 1** mirrors data identically across two or more disks, tolerating a disk failure at the cost of doubling storage requirements. **RAID 5** stripes data along with distributed parity information across three or more disks, allowing the array to survive a single disk failure while using storage more efficiently than mirroring. Different RAID levels represent different points on the trade-off between capacity, performance, and fault tolerance.

---

## 15.4 The Storage Hierarchy

Computer storage exists in a hierarchy that trades capacity for speed: CPU registers are the fastest and smallest, followed by cache memory, then main memory (RAM), then solid-state or spinning disk storage, and finally slower archival or network storage at the bottom. Each level acts as a faster, smaller cache for the level below it — frequently used disk data is cached in RAM, frequently used RAM data is cached in the CPU cache, and so on. Operating systems are deeply involved in managing several of these layers, especially the boundary between RAM and disk through virtual memory (Lesson 11) and disk caching, working to keep the most relevant data as close to the CPU as possible.

[Previous](./[14]-IO-Systems-And-Device-Management.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[16]-Protection-And-Security.md)
