[Previous](./[13]-File-Systems.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[15]-Disk-Scheduling.md)

*Storage And I/O*

# Lesson 14 - IO Systems And Device Management

## 14.1 I/O Hardware Basics

Computers interact with the outside world through a huge variety of I/O devices — keyboards, displays, disks, network cards, printers, and more — each with its own physical characteristics and speed. To manage this diversity, the OS interacts with devices through controllers, small pieces of hardware (often with their own registers and local memory) that translate between the device's specific electrical signals and a more standard interface the OS can program against. The OS talks to a controller by reading and writing its registers, which might set a mode, request an operation, or check a status.

---

## 14.2 Polling vs Interrupt-Driven I/O

There are two basic strategies for finding out when an I/O operation has completed. **Polling** has the CPU repeatedly check a device's status register in a loop until it reports readiness — simple to implement, but wasteful, since the CPU can do nothing else while it waits. **Interrupt-driven I/O** lets the CPU issue a request and move on to other work; the device raises a hardware interrupt (see Lesson 3) once it's finished, at which point the CPU pauses whatever it's doing to handle the completed operation. Interrupt-driven I/O is far more efficient for slow devices and is the standard approach in modern systems, though polling can still make sense for extremely fast operations where the interrupt overhead itself would dominate.

---

## 14.3 Direct Memory Access (DMA)

Even with interrupts, having the CPU manually copy every byte between a device and memory would waste enormous amounts of processing time for high-throughput devices like disks and network cards. Direct Memory Access (DMA) solves this by offloading bulk data transfers to a dedicated DMA controller: the CPU sets up the transfer (source, destination, and length) and then continues executing other instructions while the DMA controller moves the data directly between the device and memory, only interrupting the CPU once the entire transfer is complete. This frees the CPU almost entirely from the mechanics of large data movement.

---

## 14.4 Device Drivers

A device driver is a piece of software, usually running in or close to the kernel, that knows how to control a specific piece of hardware and exposes a standard interface for the rest of the OS to use. Drivers translate generic OS requests (like "write these bytes to this file" or "send this network packet") into the specific register operations and protocols a particular piece of hardware understands. This layered design is what lets an OS support thousands of different hardware devices from different manufacturers without every part of the kernel needing to know the details of each one — as long as a manufacturer provides a driver conforming to the OS's expected interface, their hardware can be used.

[Previous](./[13]-File-Systems.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[15]-Disk-Scheduling.md)
