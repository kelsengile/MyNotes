[Previous](./[12]-Segmentation.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[14]-IO-Systems-And-Device-Management.md)

*Storage And I/O*

# Lesson 13 - File Systems

## 13.1 What Is a File System

A file system is the part of the OS responsible for organizing, storing, and retrieving data on persistent storage devices, and for presenting that data to programs as named files rather than raw blocks of bytes on a disk. It handles allocating storage space to files, tracking which space is free, enforcing access permissions, and maintaining metadata like a file's size, owner, and modification time. By abstracting the physical layout of the storage device, the file system lets programs work with simple operations like open, read, write, and close without worrying about how or where data is physically stored.

**Example:** running `open("notes.txt")` gives a program a simple handle to read and write bytes — it never needs to know whether `notes.txt` lives on an SSD's flash cells, a spinning disk's magnetic sectors, or a network share halfway across a building.

---

## 13.2 Directory Structures

Files are organized into directories (folders), which themselves can contain other directories, forming a hierarchy. Most modern systems use a **tree-structured** directory hierarchy, where every file and directory has exactly one parent, reachable through a single path from the root. Some systems support **acyclic-graph** structures, allowing a file to be referenced from multiple locations (via links) without duplicating its data, which is convenient for sharing but requires care to avoid confusing users or breaking assumptions that every file has one true location.

```
/ (root)
├── home/
│   ├── alice/
│   │   └── report.docx
│   └── bob/
│       └── notes.txt
└── usr/
    └── bin/
```

A symbolic link at `/home/bob/shared_report.docx` pointing back to Alice's `report.docx` is an example of the acyclic-graph structure — the same data reachable from two different paths.

---

## 13.3 File Allocation Methods

The file system must decide how to lay out a file's data across the underlying storage blocks. **Contiguous allocation** stores a file's data in one unbroken run of blocks, which is fast to read sequentially but suffers from fragmentation as files grow and shrink over time. **Linked allocation** stores a file as a chain of blocks scattered anywhere on disk, each pointing to the next, which avoids fragmentation but performs poorly for random access since the chain must be followed from the start. **Indexed allocation** keeps a separate index block listing all of a file's data block locations, combining fast random access with flexible, non-contiguous storage — this is the general approach used by most modern file systems.

| Method | Sequential read | Random access | Fragmentation risk |
|---|---|---|---|
| Contiguous | Fast | Fast | High as files grow/shrink |
| Linked | Slower (must follow chain) | Very slow | None |
| Indexed | Fast | Fast | Low |

---

## 13.4 Common File Systems

Different operating systems and use cases favor different file system implementations. **NTFS** is the default on Windows, offering features like journaling (which helps recover cleanly from crashes), permissions, and encryption. **ext4** is a widely used Linux file system, also journaled, valued for its reliability and performance. **APFS** is Apple's modern file system for macOS and iOS, optimized for flash storage with features like snapshots and strong encryption support. Network and distributed file systems, such as NFS, let files stored on one machine be accessed transparently from others over a network, extending the same file abstraction across machine boundaries.

| File system | Platform | Notable feature |
|---|---|---|
| NTFS | Windows | Journaling, encryption |
| ext4 | Linux | Reliability, performance |
| APFS | macOS/iOS | Snapshots, flash optimization |
| NFS | Cross-platform (network) | Transparent remote file access |

---

[Previous](./[12]-Segmentation.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[14]-IO-Systems-And-Device-Management.md)