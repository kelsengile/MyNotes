[Previous](./[11]-Object-Storage.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[13]-Storage-Classes-and-Lifecycle-Policies.md)

*Storage*

# Lesson 12 - Block & File Storage

## 12.1 Block Storage

**Block storage** divides data into fixed-size blocks, each with its own address, and presents itself to a VM as a raw, low-level disk volume — just like a physical hard drive. The operating system formats it with a filesystem (ext4, NTFS) and treats it like local disk. Block storage volumes (e.g. AWS EBS, Azure Managed Disks) can typically be attached to only one instance at a time, offer high performance and low latency, and persist independently of the instance's lifecycle — you can detach a volume and reattach it elsewhere. This makes it the right choice for database storage, boot volumes, and any workload needing fast, consistent disk I/O.

---

## 12.2 File Storage

**File storage** provides a shared filesystem, accessible over a network protocol (NFS or SMB), that multiple instances can mount and read/write to *simultaneously* — something block storage generally can't do. It organizes data in the familiar folders-and-files hierarchy. Examples include AWS EFS, Azure Files, and Google Filestore. File storage is the right choice when several servers need to share the same files concurrently, such as a cluster of web servers serving shared user uploads, or shared configuration/content directories.

---

## 12.3 Choosing the Right Storage Type

| | Object | Block | File |
|---|---|---|---|
| Access | HTTP API | Attached disk | Network filesystem |
| Shared across instances | Yes (read) | No (one at a time) | Yes (read/write) |
| Best for | Unstructured data, backups | Databases, boot disks | Shared content, home dirs |
| Structure | Flat, key-based | Raw blocks | Folder hierarchy |

A typical web application might use block storage for its database, object storage for user-uploaded media, and file storage for a shared content directory across multiple app servers.

---

[Previous](./[11]-Object-Storage.md) | [Table of Contents](./[0]-Introduction-to-Cloud-Development.md) | [Next](./[13]-Storage-Classes-and-Lifecycle-Policies.md)
