[Previous](./[0]-Introduction-to-MacOS.md) | [Table of Contents](./[0]-Introduction-to-MacOS.md) | [Next](./[2]-The-macOS-Interface.md)

*Getting Started*

# Lesson 1 - What Is macOS

## 1.1 macOS And The Apple Ecosystem

macOS is the operating system that powers every Mac — MacBook Air, MacBook Pro, iMac, Mac mini, Mac Studio, and Mac Pro. Unlike Windows or Linux, macOS is **not sold separately or licensed to other manufacturers**. Apple builds the hardware and the software together, which is why you'll only ever find macOS running on Apple's own machines.

That "closed" relationship is also what makes the Apple ecosystem feel seamless. A Mac isn't just a computer — it's one node in a network of devices that all speak the same language:

```
        iPhone ───┐
                   │
         iPad ─────┼──── iCloud Account ──── Mac (macOS)
                   │
    Apple Watch ───┘
```

Because Apple controls the whole stack, features like copying text on your iPhone and pasting it on your Mac (Universal Clipboard), or answering a phone call from your Mac (Continuity), just work without configuration. We'll cover these Continuity features in more depth in Lesson 5.

## 1.2 macOS Version Naming And Releases

Apple ships a new major version of macOS roughly once a year, usually announced at WWDC (Worldwide Developers Conference) in June and released to the public in the fall. Each version has followed a naming pattern:

| Era | Naming Theme | Examples |
|---|---|---|
| 2001–2012 | Big Cats | Cheetah, Tiger, Leopard, Lion |
| 2013–2019 | California Landmarks | Mavericks, Yosemite, Sierra, Mojave |
| 2020–present | California Landmarks (continued) | Big Sur, Monterey, Ventura, Sonoma, Sequoia |

Alongside the name, each release has a version number (e.g., macOS 15). You can check which version your Mac is running under  **Apple menu → About This Mac**. Knowing your version matters because:

- Some apps and developer tools require a minimum macOS version.
- Older Macs eventually stop receiving new major versions, though they still get security updates for a while.
- Features described in tutorials (including this one) can shift slightly between versions — System Settings, for example, was redesigned in macOS Ventura.

## 1.3 Apple Silicon vs Intel Macs

In 2020, Apple began transitioning Macs from Intel processors to its own custom chips, known as **Apple Silicon** (the M1, M2, M3, and M4 families). This shift changed what's happening "under the hood," even though the interface looks nearly identical.

| | Intel Macs | Apple Silicon Macs |
|---|---|---|
| Chip architecture | x86_64 | ARM64 |
| Chip maker | Intel | Apple (in-house) |
| Runs iOS/iPadOS apps natively | No | Yes, for many apps |
| Battery efficiency | Lower | Significantly higher |
| Windows support | Native (Boot Camp) | Virtualization only |
| Rosetta 2 needed for older apps | No | Sometimes |

**Rosetta 2** is a translation layer that lets Apple Silicon Macs run apps built only for Intel chips, automatically converting the instructions on the fly. Most modern software now ships as a **universal binary** — a single app file containing code for both architectures — so this matters less each year, but it's still useful to understand when a developer tool refuses to install.

## 1.4 Hardware macOS Runs On

macOS is designed and tested exclusively for Apple's own hardware lineup:

- **MacBook Air / MacBook Pro** — laptops, ranging from everyday use to professional video/audio work.
- **iMac** — an all-in-one desktop with the computer built into the display.
- **Mac mini** — a compact desktop with no built-in display or keyboard.
- **Mac Studio** — a compact but high-performance desktop aimed at professionals.
- **Mac Pro** — Apple's most expandable and powerful desktop, aimed at specialized workloads.

Because Apple only needs to support a small, known set of hardware configurations, macOS can be tightly optimized — there's no equivalent to the huge variety of hardware drivers Windows has to support. This is one reason Macs are often praised for smooth performance and long battery life relative to their specs.

---

[Previous](./[0]-Introduction-to-MacOS.md) | [Table of Contents](./[0]-Introduction-to-MacOS.md) | [Next](./[2]-The-macOS-Interface.md)
