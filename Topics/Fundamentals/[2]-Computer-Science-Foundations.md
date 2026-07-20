# Computer Science Foundations

## 2.1 Number Systems (Binary, Hex, Octal)

Computers store and process everything as numbers, and specifically as **binary** (base 2) — sequences of 0s and 1s. Understanding how different number systems relate to each other is foundational to understanding how computers represent data.

### Decimal (Base 10)
The number system humans use daily, with digits 0–9. Each position represents a power of 10 (ones, tens, hundreds, ...).

### Binary (Base 2)
Uses only two digits: 0 and 1. Each position represents a power of 2. This maps directly onto hardware, where a transistor or memory cell is either "off" (0) or "on" (1) — these smallest units are called **bits**.

Example: the binary number `1011` equals:
`(1×8) + (0×4) + (1×2) + (1×1) = 11` in decimal.

Eight bits grouped together form a **byte**, which can represent 256 distinct values (0–255).

### Hexadecimal (Base 16)
Uses 16 symbols: 0–9, then A–F (representing 10–15). Hex is popular in programming because it maps very cleanly to binary — each hex digit represents exactly 4 bits (a "nibble"), so a byte can be written as just two hex digits.

Example: the byte `1111 1111` in binary is `FF` in hex, and `255` in decimal.

Hex is commonly used for things like:
- Memory addresses
- Color codes in web design (`#FF5733`)
- Debugging output

### Octal (Base 8)
Uses digits 0–7. Each position represents a power of 8. Octal was more common in early computing (particularly on systems with word sizes divisible by 3 bits) and still shows up today in specific contexts, such as Unix file permission notation (e.g., `chmod 755`).

### Why This Matters
Programmers don't typically do arithmetic in binary or hex by hand, but understanding these systems helps with:
- Debugging low-level issues (memory dumps, bitwise operations)
- Understanding data sizes and limits (e.g., why a byte maxes out at 255, or an integer at 2,147,483,647)
- Working with flags, masks, and permission systems

---

## 2.2 Character Encoding (ASCII, Unicode, UTF-8)

Computers only store numbers — so to represent text, there needs to be an agreed-upon mapping between numbers and characters. This mapping is called a **character encoding**.

### ASCII (American Standard Code for Information Interchange)
One of the earliest standardized encodings, ASCII uses 7 bits (values 0–127) to represent English letters (upper and lowercase), digits, punctuation, and some control characters (like newline and tab).

Limitation: ASCII can't represent accented letters, non-Latin scripts (Chinese, Arabic, Cyrillic, etc.), emoji, or many symbols — it was built around English-language text only.

### Unicode
Unicode was created to solve ASCII's limitations by assigning a unique number (called a **code point**) to essentially every character used in every writing system in the world, plus symbols, emoji, and more. Unicode itself is a *standard* — it defines what number maps to what character, but not how those numbers are physically stored as bytes.

### UTF-8
UTF-8 is the most widely used *encoding* of Unicode. Its key design feature is that it uses a **variable number of bytes** per character:
- Standard English/ASCII characters take just 1 byte (and are fully backward-compatible with ASCII).
- Other characters (accented letters, non-Latin scripts, emoji) take 2–4 bytes as needed.

This makes UTF-8 efficient for English-heavy text while still supporting the entire range of Unicode characters. It has become the dominant encoding on the web and in most modern software.

### Why This Matters
Character encoding issues are a common source of bugs — garbled text (sometimes called "mojibake") usually happens when data encoded one way (e.g., UTF-8) is read using a different encoding assumption (e.g., Latin-1). Understanding encoding is essential for anything involving text processing, file I/O, or networking across different systems and languages.

---

## 2.3 Basic Computer Architecture (CPU, RAM, Storage, Cache)

At a high level, a computer's architecture can be thought of as a hierarchy of components balancing **speed** against **capacity/cost**.

### CPU (Central Processing Unit)
The component that actually executes instructions, as covered in Section 1.2. Modern CPUs contain multiple **cores**, each capable of executing instructions independently, enabling true parallel processing.

### Cache
Small, extremely fast memory built directly into or very close to the CPU. Because RAM, while fast, is still slower than the CPU itself, cache stores frequently accessed data and instructions so the CPU doesn't have to wait as long to fetch them. Cache is typically organized in levels:
- **L1** — smallest and fastest, closest to the CPU core
- **L2** — larger, slightly slower
- **L3** — larger still, often shared across multiple cores

### RAM (Random Access Memory)
Main working memory, used to hold data and instructions for programs that are currently running. It's much larger than cache but slower to access. RAM is **volatile** — its contents are lost when power is removed.

### Storage (HDD/SSD)
Persistent memory used to store data long-term (operating system, files, installed programs). Unlike RAM, storage is **non-volatile** — data remains after power off.
- **HDDs (Hard Disk Drives)** use spinning magnetic disks — cheaper per gigabyte, but slower.
- **SSDs (Solid State Drives)** use flash memory with no moving parts — significantly faster, though historically more expensive per gigabyte (that gap has narrowed considerably).

### The Memory Hierarchy
Putting it together, from fastest/smallest/most expensive to slowest/largest/cheapest:

```
Registers → Cache (L1 → L2 → L3) → RAM → SSD/HDD Storage
```

This hierarchy exists because it's not economically or physically practical to have huge amounts of extremely fast memory. Instead, computers keep frequently needed data as close to the CPU as possible, and push less frequently needed data further down the hierarchy.

---

## 2.4 Operating System Basics (Processes, File Systems, Permissions)

The **operating system (OS)** — such as Windows, macOS, or Linux — is the software layer that manages hardware resources and provides services that applications rely on, so individual programs don't need to manage hardware directly.

### Processes
A **process** is an instance of a running program. The OS is responsible for:
- Allocating CPU time to each process (**scheduling**), especially important since a CPU core can technically only execute one instruction stream at a time, per core.
- Allocating and isolating memory for each process, so one program can't (under normal circumstances) read or corrupt another program's memory.
- Managing the lifecycle of a process: creation, running, waiting, and termination.

A related concept is a **thread** — a smaller unit of execution within a process. A single process can have multiple threads running concurrently, sharing the same memory space, which enables parallelism within one program.

### File Systems
The **file system** defines how data is organized, named, and stored on a storage device. It provides the abstraction of "files" and "folders/directories" on top of the raw bytes stored on disk. Common file systems include NTFS (Windows), APFS (macOS), and ext4 (Linux).

Key file system responsibilities:
- Tracking where each file's data physically lives on the storage device
- Managing free space
- Supporting operations like creating, reading, updating, deleting, and renaming files

### Permissions
Since multiple users (and multiple programs) may share a single computer or server, operating systems implement **permissions** to control who/what can access or modify a given file or resource.

On Unix-like systems (Linux, macOS), permissions are typically expressed for three categories — **owner**, **group**, and **others** — with three types of access each: **read (r)**, **write (w)**, and **execute (x)**. This is often represented in a compact numeric form (recall octal from Section 2.1!), such as `755` or `644`.

### Why This Matters
Understanding processes, file systems, and permissions helps explain everyday programming concerns: why a program crashes with "permission denied," how multiple programs can run "at once" on one CPU core, why file paths differ between operating systems, and how to reason about concurrency and resource sharing in software you write.