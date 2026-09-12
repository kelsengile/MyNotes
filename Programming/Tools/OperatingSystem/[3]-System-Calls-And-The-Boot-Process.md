[Previous](./[2]-OS-Architecture-And-Structure.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[4]-Processes-And-Process-States.md)

*Foundations*

# Lesson 3 - System Calls And The Boot Process

## 3.1 What Is a System Call

A system call is the mechanism a user-space program uses to request a service from the kernel, such as reading a file, allocating memory, or creating a new process. Because user programs run with restricted privileges, they can't perform these operations directly — instead, they execute a special instruction that switches the CPU into kernel mode, hands control to the OS, and lets the kernel carry out the request safely. Once the kernel finishes, control returns to the user program along with a result. Common categories of system calls include process control, file management, device management, and communication.

| Category | Example system call | Purpose |
|---|---|---|
| Process control | `fork()`, `exit()` | Create or end a process |
| File management | `open()`, `read()`, `write()` | Work with files |
| Device management | `ioctl()` | Configure a device |
| Communication | `send()`, `recv()` | Exchange data between processes |

---

## 3.2 The Boot Sequence

Booting is the process of starting a computer and loading the operating system into memory. It typically begins with firmware (BIOS or UEFI) built into the motherboard, which runs a power-on self-test and then locates a bootloader on a storage device. The bootloader's job is to load the OS kernel into memory and hand off control to it. From there, the kernel initializes core subsystems — memory management, device drivers, the file system — and eventually starts the first user-space process, which in turn launches the rest of the system's services and, finally, the login or desktop environment the user interacts with.

**Simplified boot sequence:**

```
1. Power on -> firmware (BIOS/UEFI) runs self-test
2. Firmware locates and runs the bootloader
3. Bootloader loads the OS kernel into memory
4. Kernel initializes memory, drivers, file system
5. Kernel starts the first user-space process (init/systemd)
6. init launches remaining services
7. Login screen or desktop environment appears
```

---

## 3.3 Interrupts And Traps

Interrupts are signals that pause the CPU's current execution to handle an event that needs immediate attention, such as a keystroke, a network packet arriving, or a disk finishing a read. Hardware interrupts come from devices, while traps (or exceptions) are triggered by the CPU itself in response to events like a system call, a division by zero, or an invalid memory access. In both cases, the CPU saves its current state, jumps to a predefined interrupt handler in the kernel, and resumes the interrupted work once the handler finishes. Interrupts are what let an OS respond quickly to the outside world instead of constantly checking for events itself.

| Trigger | Type | Example |
|---|---|---|
| A key is pressed | Hardware interrupt | Keyboard controller signals the CPU |
| A disk finishes reading | Hardware interrupt | Disk controller signals completion |
| Division by zero | Trap/exception | CPU itself detects the invalid operation |
| A program calls `read()` | Trap (system call) | CPU switches to kernel mode to service it |

---

## 3.4 Dual Mode Operation

To protect the system, CPUs support at least two operating modes: kernel mode (also called supervisor or privileged mode) and user mode. In kernel mode, code can execute any instruction and access any memory address; in user mode, certain instructions are forbidden and memory access is restricted to what the process owns. This dual mode operation, enforced by the hardware itself, is what makes the kernel/user-space separation from Lesson 2 actually enforceable — a user program cannot simply decide to access forbidden memory or hardware, because the CPU will refuse and trap into the kernel instead.

| Mode | Who runs here | Can do |
|---|---|---|
| Kernel mode | The OS kernel | Execute any instruction, access any memory |
| User mode | Ordinary applications | Only permitted instructions, only owned memory |

---

[Previous](./[2]-OS-Architecture-And-Structure.md) | [Table of Contents](./[0]-Introduction-to-OperatingSystems.md) | [Next](./[4]-Processes-And-Process-States.md)