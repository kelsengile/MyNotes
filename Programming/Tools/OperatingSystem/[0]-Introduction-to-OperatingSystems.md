[⬅ Back to README](../../../README.md)

# Introduction to Operating Systems

An operating system (OS) is the software layer that sits between raw computer hardware and the applications people actually use — it manages the CPU, memory, storage, and I/O devices, and gives programs a consistent, safe way to use them without every application having to speak directly to hardware. This Topic builds up the fundamental, system-agnostic concepts behind operating systems from the ground up: what an OS actually does, how it manages processes and memory, how it handles storage and devices, and how modern systems layer in security and virtualization.

These lessons intentionally avoid tying the concepts to any single operating system. Once you understand the fundamentals here, you'll be equipped to reason about Linux, Windows, macOS, or any other OS and recognize the same ideas under different names and implementations.

## Table of Contents

**Foundations**

   1. **[What Is an Operating System?](./[1]-What-Is-An-Operating-System.md)**  
       1.1 Defining an Operating System  
       1.2 The OS as Resource Manager  
       1.3 The OS as Abstraction Layer  
       1.4 A Brief History of Operating Systems  
   2. **[OS Architecture And Structure](./[2]-OS-Architecture-And-Structure.md)**  
       2.1 Kernel vs User Space  
       2.2 Monolithic Kernels  
       2.3 Microkernels  
       2.4 Hybrid And Modular Kernels  
   3. **[System Calls And The Boot Process](./[3]-System-Calls-And-The-Boot-Process.md)**  
       3.1 What Is a System Call  
       3.2 The Boot Sequence  
       3.3 Interrupts And Traps  
       3.4 Dual Mode Operation  

**Process Management**

   4. **[Processes And Process States](./[4]-Processes-And-Process-States.md)**  
       4.1 What Is a Process  
       4.2 The Process Control Block  
       4.3 Process States And Transitions  
       4.4 Context Switching  
   5. **[Threads And Multithreading](./[5]-Threads-And-Multithreading.md)**  
       5.1 What Is a Thread  
       5.2 Processes vs Threads  
       5.3 User-Level vs Kernel-Level Threads  
       5.4 Multithreading Models  
   6. **[CPU Scheduling](./[6]-CPU-Scheduling.md)**  
       6.1 Scheduling Goals And Criteria  
       6.2 Common Scheduling Algorithms  
       6.3 Preemptive vs Non-Preemptive Scheduling  
       6.4 Multilevel Queue Scheduling  

**Concurrency And Synchronization**

   7. **[Process Synchronization](./[7]-Process-Synchronization.md)**  
       7.1 The Critical Section Problem  
       7.2 Locks And Mutexes  
       7.3 Semaphores  
       7.4 Monitors  
   8. **[Deadlocks](./[8]-Deadlocks.md)**  
       8.1 Conditions For Deadlock  
       8.2 Deadlock Prevention  
       8.3 Deadlock Avoidance  
       8.4 Deadlock Detection And Recovery  
   9. **[Interprocess Communication](./[9]-Interprocess-Communication.md)**  
       9.1 Shared Memory  
       9.2 Message Passing  
       9.3 Pipes And Sockets  
       9.4 Signals  

**Memory Management**

   10. **[Memory Management Basics](./[10]-Memory-Management-Basics.md)**  
       10.1 Address Spaces  
       10.2 Contiguous Memory Allocation  
       10.3 Fragmentation  
       10.4 Swapping  
   11. **[Virtual Memory And Paging](./[11]-Virtual-Memory-And-Paging.md)**  
       11.1 What Is Virtual Memory  
       11.2 Paging And Page Tables  
       11.3 Page Replacement Algorithms  
       11.4 Thrashing  
   12. **[Segmentation](./[12]-Segmentation.md)**  
       12.1 What Is Segmentation  
       12.2 Segmentation vs Paging  
       12.3 Segmented Paging  
       12.4 Protection And Sharing  

**Storage And I/O**

   13. **[File Systems](./[13]-File-Systems.md)**  
       13.1 What Is a File System  
       13.2 Directory Structures  
       13.3 File Allocation Methods  
       13.4 Common File Systems  
   14. **[IO Systems And Device Management](./[14]-IO-Systems-And-Device-Management.md)**  
       14.1 I/O Hardware Basics  
       14.2 Polling vs Interrupt-Driven I/O  
       14.3 Direct Memory Access (DMA)  
       14.4 Device Drivers  
   15. **[Disk Scheduling](./[15]-Disk-Scheduling.md)**  
       15.1 Disk Structure Basics  
       15.2 Disk Scheduling Algorithms  
       15.3 RAID  
       15.4 The Storage Hierarchy  

**Security And Advanced Topics**

   16. **[Protection And Security](./[16]-Protection-And-Security.md)**  
       16.1 Goals Of Protection  
       16.2 Authentication And Access Control  
       16.3 Common Security Threats  
       16.4 Security Mechanisms  
   17. **[Virtualization](./[17]-Virtualization.md)**  
       17.1 What Is Virtualization  
       17.2 Hypervisors  
       17.3 Containers vs Virtual Machines  
       17.4 Cloud Computing And The OS  

**Next Steps**

   18. **[Choosing And Exploring Operating Systems](./[18]-Choosing-And-Exploring-Operating-Systems.md)**  
       18.1 Major OS Families  
       18.2 Linux, Windows, And macOS Compared  
       18.3 Learning Resources  
       18.4 Where To Go Next  

**## Operating Systems**

The lessons above introduce the fundamentals of operating systems, including system resources, processes, memory management, file systems, and user interaction. To see these concepts applied to specific, widely-used operating systems, continue on to:

* **[Linux](./Linux/Introduction_to_Linux.md)** — an open-source operating system known for its flexibility, security, customization, and widespread use in servers, development, and embedded systems.

* **[MacOS](./MacOS/Introduction_to_MacOS.md)** — Apple's operating system designed for Mac computers, providing a polished user experience and strong integration with Apple's hardware and software ecosystem.

* **[Windows](./Windows/Introduction_to_Windows.md)** — Microsoft's widely-used desktop operating system, known for broad hardware and software compatibility and its extensive use in personal, business, and gaming environments.

