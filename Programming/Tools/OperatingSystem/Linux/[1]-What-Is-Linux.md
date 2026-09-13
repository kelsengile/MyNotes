[Table of Contents](./[0]-Introduction-to-Linux.md) | [Next](./[2]-Installing-And-Trying-Linux.md)

*Getting Started*

# Lesson 1 - What Is Linux

## 1.1 The Linux Kernel vs A Distribution

Strictly speaking, "Linux" refers only to the **kernel** — the core program that manages a computer's CPU, memory, and hardware devices, mediating between running programs and the physical machine.

```
┌─────────────────────────────────────┐
│         Applications                  │  ← Firefox, VS Code, bash
├─────────────────────────────────────┤
│    Shell, Desktop Environment          │  ← bash, GNOME, KDE
├─────────────────────────────────────┤
│   System Libraries & Utilities        │  ← glibc, coreutils
├─────────────────────────────────────┤
│         Linux Kernel                  │  ← manages CPU, memory, drivers
├─────────────────────────────────────┤
│            Hardware                   │
└─────────────────────────────────────┘
```

A **distribution ("distro")** bundles the Linux kernel together with everything needed to make a usable operating system: a package manager, system utilities, a default shell, and often a desktop environment. This is why "Ubuntu" and "Fedora" can look and feel completely different from each other while both being "Linux" underneath — they share the same kernel but package very different software around it.

---

## 1.2 Open Source Philosophy

Linux is released under the **GNU General Public License (GPL)**, meaning its source code is freely available for anyone to read, modify, and redistribute. This traces back to the broader **free and open-source software (FOSS)** movement, which Linux is one of the most visible successes of.

Key ideas behind the philosophy:

- **Transparency** — anyone can inspect exactly what the code does, which is a major reason security-conscious organizations (governments, banks, infrastructure providers) favor Linux for critical systems.
- **No single point of control** — no single company can unilaterally discontinue Linux, change its license, or lock users out, since the code and its history remain publicly available regardless of what any one contributor or company does.
- **Community-driven development** — thousands of contributors worldwide, from individual hobbyists to employees of companies like Red Hat, Google, and Intel, submit changes that are reviewed and merged by kernel maintainers.
- **"Free" means freedom, not always free-of-cost** — most distributions are also free-of-cost, but the GPL's core guarantee is about the freedom to use, study, modify, and share the software, not the price tag.

---

## 1.3 Common Distributions (Ubuntu, Fedora, Debian, Arch)

Distributions differ mainly in their package manager, release philosophy, and target audience:

| Distribution | Based On | Package Manager | Release Style | Known For |
|---|---|---|---|---|
| Ubuntu | Debian | apt | Fixed releases (every 6 months, LTS every 2 years) | Beginner-friendly, huge community |
| Debian | (original) | apt | Very stable, infrequent releases | Rock-solid stability, servers |
| Fedora | (original, sponsored by Red Hat) | dnf | Fast-moving, ~6 month releases | Cutting-edge packages, upstream for RHEL |
| Arch Linux | (original) | pacman | Rolling release (continuous updates) | Full control, minimal defaults, DIY setup |

```
Debian ──────▶ Ubuntu ──────▶ Linux Mint, Pop!_OS, elementary OS
Fedora ──────▶ (upstream for) RHEL, CentOS Stream, Rocky Linux
Arch ────────▶ Manjaro, EndeavourOS
```

Many popular distributions are themselves built **on top of** one of these "upstream" distributions, inheriting their package manager and much of their underlying structure while customizing the desktop experience and defaults.

---

## 1.4 Choosing A Distribution

For most people learning Linux, the "best" distribution is the one that gets out of the way and lets you focus on learning the underlying concepts rather than fighting the OS itself.

Rough guidance by goal:

- **First time trying Linux at all** — **Ubuntu** or **Linux Mint**: large communities, extensive documentation, and broad hardware/driver support out of the box.
- **Server / DevOps / production use** — **Ubuntu Server**, **Debian**, or **Rocky Linux/AlmaLinux** (free, RHEL-compatible): valued for stability and long-term support.
- **Wanting to deeply understand how Linux works, and comfortable troubleshooting** — **Arch Linux**: a minimal starting point where you build up the system piece by piece, learning what each component does along the way.
- **Matching a specific employer/team's environment** — sometimes the right answer is simply "whatever your team already uses," since package manager commands and system layout differ enough between families that matching your production environment locally saves friction later.

Whatever you choose first, the core skills in this Topic — the file system, permissions, the shell, and process management — transfer almost entirely between distributions; only the package manager commands (Lesson 5) meaningfully differ.

[Table of Contents](./[0]-Introduction-to-Linux.md) | [Next](./[2]-Installing-And-Trying-Linux.md)
