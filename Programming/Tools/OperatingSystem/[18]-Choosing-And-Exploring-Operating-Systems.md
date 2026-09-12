[Previous](./[17]-Virtualization.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md)

*Next Steps*

# Lesson 18 - Choosing And Exploring Operating Systems

## 18.1 Major OS Families

Most operating systems in use today trace back to one of a few major lineages. The **Unix family** includes Linux (used widely on servers, embedded devices, and increasingly desktops) and BSD-derived systems, and emphasizes a modular, "everything is a file" design philosophy. **Windows NT**, developed by Microsoft, dominates desktop and enterprise environments and prioritizes broad hardware and software compatibility. **Apple's Darwin-based systems** (macOS, iOS, iPadOS) blend a Unix-like BSD core with a proprietary graphical layer. Mobile operating systems like Android (built on the Linux kernel) and iOS apply these same fundamental OS concepts within constraints specific to battery life, touch input, and app sandboxing.

A quick map of who's who in each lineage:

| Family | Example Systems | Kernel | Typical Use |
|---|---|---|---|
| Unix / Unix-like | Ubuntu, Fedora, Arch, Debian, FreeBSD | Linux kernel or BSD kernel | Servers, cloud, embedded, developer desktops |
| Windows NT | Windows 10, Windows 11, Windows Server | NT kernel | Desktops, gaming, enterprise |
| Darwin-based | macOS, iOS, iPadOS | XNU (hybrid Mach/BSD) kernel | Apple desktops, phones, tablets |
| Linux-derived mobile | Android, ChromeOS | Linux kernel | Phones, tablets, low-cost laptops |

The Unix philosophy of small, composable tools shows up directly in the shell. A single line like:

```bash
ls -l /var/log | grep "error" | wc -l
```

lists files, filters the lines containing `"error"`, and counts them — three tiny programs chained together with pipes (`|`) instead of one large program trying to do everything. Windows and macOS both include full graphical file explorers for the same task, but also ship their own command-line shells (PowerShell on Windows, the Terminal app with `zsh` or `bash` on macOS) that follow this same piping idea.

---

## 18.2 Linux, Windows, And macOS Compared

Linux is open-source, highly configurable, and dominant on servers and in cloud infrastructure, but its many distributions and configuration options mean it can be less uniform for beginners. Windows offers the widest compatibility with commercial and gaming software and a consistent, polished experience, but is closed-source and historically has had a larger surface area for malware given its market share. macOS provides a tightly integrated hardware-software experience with strong design consistency and Unix-based tools under the hood, but runs only on Apple hardware and has a narrower (though growing) software catalog for certain specialized use cases. None of these is strictly "best" — the right choice depends on the hardware available, the software you need to run, and how much control versus convenience you want.

| Criteria | Linux | Windows | macOS |
|---|---|---|---|
| Source model | Open-source | Closed-source | Closed-source (Unix core is open) |
| Hardware | Runs on almost anything | Runs on almost anything | Apple hardware only |
| Customization | Very high | Moderate | Low to moderate |
| Gaming/commercial software | Improving, still limited | Widest support | Narrower catalog |
| Package management | `apt`, `dnf`, `pacman`, etc. | Winget, Microsoft Store, installers | Homebrew, App Store, installers |
| Best fit | Servers, developers, tinkerers | General desktop, gaming, business | Creative work, tight hardware/software integration |

The differences show up even in something as simple as asking "what OS am I running, and what version?":

```bash
# Linux
cat /etc/os-release

# Windows (PowerShell)
Get-ComputerInfo | Select-Object WindowsProductName, WindowsVersion

# macOS
sw_vers
```

Same underlying question, three different tools — a small illustration of how each OS exposes its internals in a way consistent with its overall philosophy (Linux: plain text files, Windows: structured cmdlets, macOS: purpose-built utilities).

---

## 18.3 Learning Resources

To go deeper on operating systems, hands-on experimentation is invaluable: try installing a Linux distribution in a virtual machine (tying directly into Lesson 17) and explore its command line, process tools, and file system directly. Classic textbooks like *Operating System Concepts* (Silberschatz, Galvin, and Gagne) and *Modern Operating Systems* (Tanenbaum) cover these fundamentals in significantly more mathematical and implementation depth. Reading the source code or documentation of a real, widely-used kernel — Linux's is entirely open — is one of the best ways to see these abstract concepts implemented in practice.

A few concrete starting points:

- **Distros to try first:** Ubuntu or Linux Mint (beginner-friendly), Fedora (closer to upstream Linux), Arch (build-it-yourself, steep learning curve but great for understanding internals).
- **Kernel source:** browse [kernel.org](https://kernel.org) or the Linux kernel's `mm/` (memory management) and `kernel/sched/` (scheduler) directories to see the exact concepts from earlier lessons in real C code.
- **Guided practice:** work through [Exercise 18 - Exploring Your OS](./Exercise-18-Exploring-Your-OS.sh) to try the commands from this lesson yourself in a VM.

---

## 18.4 Where To Go Next

With the fundamentals in this Topic in hand, you're equipped to explore OS-specific coursework and documentation, systems programming languages like C or Rust that expose these concepts more directly, or adjacent fields like computer networks, distributed systems, and computer architecture that build on the same foundation of processes, memory, and hardware interaction. The concepts covered here — processes, memory, scheduling, concurrency, storage, and security — form the backbone of virtually all software that runs above the hardware layer, so they'll keep showing up no matter which direction you take next.

Some concrete "next" directions, depending on what interests you most:

- **Curious about internals?** Build a toy OS component, like a simple scheduler or shell, in C.
- **Curious about infrastructure?** Learn Linux server administration — SSH, systemd, and basic networking are a natural next step.
- **Curious about security?** OS security concepts (permissions, sandboxing, privilege escalation) connect directly to 18.1's discussion of app sandboxing on mobile OSes.
- **Curious about hardware?** Pair this Topic with a computer architecture course to see how the OS talks to the CPU, memory, and storage it manages.

[Previous](./[17]-Virtualization.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md)