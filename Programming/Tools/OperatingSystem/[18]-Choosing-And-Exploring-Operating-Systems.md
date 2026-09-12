[Previous](./[17]-Virtualization.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md)

*Next Steps*

# Lesson 18 - Choosing And Exploring Operating Systems

## 18.1 Major OS Families

Most operating systems in use today trace back to one of a few major lineages. The **Unix family** includes Linux (used widely on servers, embedded devices, and increasingly desktops) and BSD-derived systems, and emphasizes a modular, "everything is a file" design philosophy. **Windows NT**, developed by Microsoft, dominates desktop and enterprise environments and prioritizes broad hardware and software compatibility. **Apple's Darwin-based systems** (macOS, iOS, iPadOS) blend a Unix-like BSD core with a proprietary graphical layer. Mobile operating systems like Android (built on the Linux kernel) and iOS apply these same fundamental OS concepts within constraints specific to battery life, touch input, and app sandboxing.

---

## 18.2 Linux, Windows, And macOS Compared

Linux is open-source, highly configurable, and dominant on servers and in cloud infrastructure, but its many distributions and configuration options mean it can be less uniform for beginners. Windows offers the widest compatibility with commercial and gaming software and a consistent, polished experience, but is closed-source and historically has had a larger surface area for malware given its market share. macOS provides a tightly integrated hardware-software experience with strong design consistency and Unix-based tools under the hood, but runs only on Apple hardware and has a narrower (though growing) software catalog for certain specialized use cases. None of these is strictly "best" — the right choice depends on the hardware available, the software you need to run, and how much control versus convenience you want.

---

## 18.3 Learning Resources

To go deeper on operating systems, hands-on experimentation is invaluable: try installing a Linux distribution in a virtual machine (tying directly into Lesson 17) and explore its command line, process tools, and file system directly. Classic textbooks like *Operating System Concepts* (Silberschatz, Galvin, and Gagne) and *Modern Operating Systems* (Tanenbaum) cover these fundamentals in significantly more mathematical and implementation depth. Reading the source code or documentation of a real, widely-used kernel — Linux's is entirely open — is one of the best ways to see these abstract concepts implemented in practice.

---

## 18.4 Where To Go Next

With the fundamentals in this Topic in hand, you're equipped to explore OS-specific coursework and documentation, systems programming languages like C or Rust that expose these concepts more directly, or adjacent fields like computer networks, distributed systems, and computer architecture that build on the same foundation of processes, memory, and hardware interaction. The concepts covered here — processes, memory, scheduling, concurrency, storage, and security — form the backbone of virtually all software that runs above the hardware layer, so they'll keep showing up no matter which direction you take next.

[Previous](./[17]-Virtualization.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md)
