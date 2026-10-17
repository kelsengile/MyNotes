[Previous](./[4]-Number-Systems.md) | [Table of Contents](./[0]-Introduction-to-Assembly.md) | [Next](./[6]-Registers.md)

*Foundations*

# Lesson 5 - How a CPU Works (fetch, decode, execute)

To write good assembly you need a mental model of the machine that executes it. This lesson explains what a CPU does at its most basic level, how its parts cooperate with memory, how instruction sets differ, and how an instruction becomes bytes.

---

## 5.1 The Fetch-Decode-Execute Cycle

A CPU repeats one simple loop billions of times per second:

```
        ┌────────────────────────────────────────────┐
        ▼                                            │
   ┌─────────┐     ┌─────────┐     ┌─────────┐      │
   │  FETCH  │ ──► │ DECODE  │ ──► │ EXECUTE │ ─────┘
   └─────────┘     └─────────┘     └─────────┘
 read instruction   work out what    do it, store the
 at address in RIP  it means         result, update RIP
```

1. **Fetch:** the CPU reads the instruction bytes from memory at the address held in the **instruction pointer** (`rip` on x86-64).
2. **Decode:** the control unit works out which operation it is and which operands (registers, memory, constants) it uses.
3. **Execute:** the operation is performed, for example by the arithmetic unit. Results are written to a register or to memory, and **flags** may be updated.
4. `rip` is advanced to the next instruction (or set to a new address by a jump, `call`, or `ret`) and the cycle repeats.

This explains several things you will see later:

- A **jump** is simply an instruction that changes `rip`.
- A **crash** often means `rip` pointed at memory that is not executable or not valid.
- A **debugger** works by stopping the cycle after each instruction (`stepi`).

Modern CPUs do far more than this simple model: they overlap many instructions in a **pipeline**, execute them **out of order**, and **predict** branches (Lesson 37). But the *observable behaviour* is always as if instructions ran one at a time, in order.

---

## 5.2 Registers, ALU, and Memory

### The main components

```
          ┌─────────────────────── CPU ─────────────────────────┐
          │  ┌──────────────┐    ┌────────────────────────────┐  │
          │  │ Control Unit │    │ Registers                  │  │
          │  │ (fetch,      │◄──►│ rax rbx rcx ... r15        │  │
          │  │  decode)     │    │ rip, rflags                │  │
          │  └──────┬───────┘    └─────────────┬──────────────┘  │
          │         │                          │                 │
          │         ▼                          ▼                 │
          │  ┌──────────────────────────────────────────┐        │
          │  │ ALU (arithmetic logic unit)              │        │
          │  └──────────────────────────────────────────┘        │
          │  ┌──────────────────────┐                            │
          │  │ L1 / L2 / L3 caches  │                            │
          │  └──────────┬───────────┘                            │
          └─────────────┼────────────────────────────────────────┘
                        │  memory bus
                  ┌─────▼─────┐
                  │   RAM     │
                  └───────────┘
```

- **Registers** are tiny, extremely fast storage slots *inside* the CPU. x86-64 has 16 general-purpose 64-bit registers. Most computation happens on registers.
- The **ALU** (Arithmetic Logic Unit) performs addition, subtraction, AND, OR, shifts, comparisons, and so on. It takes values from registers and updates the **flags**.
- The **control unit** fetches and decodes instructions and directs everything else.
- **Caches** hold copies of recently used memory close to the CPU.
- **Memory (RAM)** is large but slow. It is an array of bytes, each with a numeric **address**.

### A speed comparison

Approximate access times (they vary by CPU):

| Storage | Typical latency | Typical size |
|---------|-----------------|--------------|
| Register | under 1 ns (about 1 cycle) | 16 × 8 bytes |
| L1 cache | about 1 ns | 32-64 KB |
| L2 cache | about 3-5 ns | 256 KB - 2 MB |
| L3 cache | about 10-20 ns | several MB to tens of MB |
| RAM | about 50-100 ns | GBs |

This is why assembly code tries to keep values in registers and why the number of memory accesses often decides performance.

### Memory is a numbered array of bytes

```
Address :  0x1000  0x1001  0x1002  0x1003  0x1004  ...
Content :   0x48    0x65    0x6C    0x6C    0x6F   ...   ("Hello")
```

An **address** is just a number. Pointers are numbers that hold addresses, and registers can hold them. Reading and writing memory means giving the CPU an address and a size.

### Virtual memory

Your program does not see physical RAM directly. The operating system and the CPU's **memory management unit (MMU)** give each process its own **virtual address space**. Pages (usually 4 KB) are mapped to physical memory and carry permission bits: readable, writable, executable. That is why writing to your code or executing your data causes a **segmentation fault**.

---

## 5.3 Instruction Sets and Architectures

An **instruction set architecture (ISA)** is the contract between software and hardware: which instructions exist, what registers there are, and how memory behaves. Different CPUs implement the same ISA differently (Intel and AMD both implement x86-64), but a program written for one ISA cannot run natively on another.

| ISA | Style | Found in |
|-----|-------|----------|
| **x86 / x86-64** | CISC | Desktops, laptops, servers |
| **ARM (AArch64)** | RISC | Phones, Apple Silicon, many servers, Raspberry Pi |
| **RISC-V** | RISC (open standard) | Embedded, research, growing in industry |
| **MIPS, PowerPC** | RISC | Legacy and embedded systems |

### CISC vs RISC

- **CISC** (Complex Instruction Set Computer, e.g. x86): many instructions, variable length (1 to 15 bytes), and instructions that can use **memory operands directly** (`add rax, [rbx]`).
- **RISC** (Reduced Instruction Set Computer, e.g. ARM64, RISC-V): fewer, simpler, usually **fixed-length** instructions (4 bytes), and a **load/store** design where only dedicated load and store instructions access memory.

Internally, modern x86 CPUs translate complex instructions into simpler **micro-operations**, so the gap in practice is smaller than the theory suggests. Lesson 28 covers ARM64 in more depth.

### x86-64 history in brief

| Year | Milestone |
|------|-----------|
| 1978 | Intel 8086: 16-bit |
| 1985 | 80386: 32-bit (`eax`, `esp`, ...) |
| 2003 | AMD64: 64-bit extension (`rax`, `r8`-`r15`) |

Each generation kept backward compatibility, which is why you will see `ax`, `eax`, and `rax` all in the same register family (Lesson 6).

---

## 5.4 Instruction Encoding and Machine Code

Every instruction is stored as bytes. You rarely write them by hand, but understanding the structure helps you read disassembly and shellcode.

An x86-64 instruction has up to these parts:

```
┌──────────┬─────┬────────┬────────┬─────┬──────────────┬───────────┐
│ Prefixes │ REX │ Opcode │ ModR/M │ SIB │ Displacement │ Immediate │
│ (opt.)   │(opt)│ 1-3 B  │ (opt.) │(opt)│   (opt.)     │  (opt.)   │
└──────────┴─────┴────────┴────────┴─────┴──────────────┴───────────┘
```

- **Opcode:** identifies the operation (`0x01` is "add r/m, reg").
- **ModR/M:** one byte that says which registers or memory operand are used.
- **SIB:** extra byte for complex addressing like `[rax + rbx*4]`.
- **REX prefix** (`0x40`-`0x4F`): extends to 64-bit operand size and to the registers `r8`-`r15`.
- **Displacement / Immediate:** constant offsets and constant values, stored little-endian.

### Examples

You can see all of these by assembling a file and reading the listing (`nasm -l`) or by using `objdump -d`:

| Assembly | Machine code (hex) | Explanation |
|----------|--------------------|-------------|
| `nop` | `90` | One byte, does nothing |
| `ret` | `C3` | Return |
| `syscall` | `0F 05` | Two-byte opcode |
| `mov eax, 1` | `B8 01 00 00 00` | `B8` = "mov eax, imm32" followed by 1 in little-endian |
| `add rax, rbx` | `48 01 D8` | `48` REX.W (64-bit), `01` add, `D8` ModR/M (source rbx, dest rax) |
| `add rax, 3` | `48 83 C0 03` | `83` = "add r/m, imm8" with `C0` selecting rax, `03` is the constant |
| `xor eax, eax` | `31 C0` | Shortest way to zero a register |
| `jmp short +5` | `EB 05` | Relative jump by 5 bytes |

Notice that `mov eax, 1` (5 bytes) and `xor eax, eax` (2 bytes) are different sizes. Instruction length affects code size and instruction-cache use, which is one reason experienced programmers write `xor eax, eax` to zero a register.

### Relative addressing

Jumps and calls do not store absolute addresses. They store a **displacement relative to the next instruction**. That makes code **position-independent**: it works wherever it is loaded in memory.

```
401000:  EB 05        jmp 401007      ; 0x401002 (next instruction) + 5 = 0x401007
```

### Machine code is just data

The CPU makes no distinction between code and data. If `rip` points at the bytes `B8 01 00 00 00`, they are executed as `mov eax, 1`. Operating systems use page permissions (the **NX** bit) to forbid executing data pages, a security defence you will meet again in Lessons 34 and 35.

---

[Previous](./[4]-Number-Systems.md) | [Table of Contents](./[0]-Introduction-to-Assembly.md) | [Next](./[6]-Registers.md)
