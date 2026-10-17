[Previous](./[5]-How-a-CPU-Works.md) | [Table of Contents](./[0]-Introduction-to-Assembly.md) | [Next](./[7]-Syntax-and-Structure.md)

*Foundations*

# Lesson 6 - Registers & the x86-64 Architecture

Registers are the CPU's working variables. Nearly every instruction you write reads or writes them, so knowing their names, sizes, and special roles is essential. This lesson covers the x86-64 register set in detail.

---

## 6.1 General-Purpose Registers

x86-64 has **16 general-purpose registers (GPRs)**, each 64 bits wide:

| Register | Traditional role | Notes |
|----------|------------------|-------|
| `rax` | **Accumulator** | Return values; implicit operand of `mul`, `div`; syscall number |
| `rbx` | **Base** | Free to use; preserved across calls (callee-saved) |
| `rcx` | **Counter** | Loop counts, shift counts, `rep` count; 4th argument |
| `rdx` | **Data** | Extends `rax` in `mul`/`div`; 3rd argument |
| `rsi` | **Source index** | String source; 2nd argument |
| `rdi` | **Destination index** | String destination; 1st argument |
| `rbp` | **Base pointer** | Frame pointer (Lesson 15); callee-saved |
| `rsp` | **Stack pointer** | Always points at the top of the stack; do not use as a general register |
| `r8` - `r15` | none | Eight extra registers added by x86-64 |

Most of the "traditional roles" are historical. Only a few remain *hard-wired* in the hardware:

- `rsp` is used implicitly by `push`, `pop`, `call`, and `ret`.
- `rax` and `rdx` are used implicitly by `mul`, `imul` (one-operand form), `div`, and `idiv`.
- `rcx` is the count for `loop`, `rep`, and variable shifts (`cl`).
- `rsi`, `rdi`, `rcx`, and `rax` are used by the string instructions (Lesson 14).
- `rax`, `rcx`, `rdx`, `rsi`, `rdi`, `r8`-`r11` have roles in the Linux **system call** and **calling** conventions (Lessons 17 and 19).

Everything else is a convention you can break in your own code, but breaking a convention in code that calls other people's code leads to bugs.

> **ARM64 note:** ARM64 has 31 general-purpose registers named `x0`-`x30` (64-bit) with `w0`-`w30` as their 32-bit halves, plus a dedicated stack pointer `sp` and a zero register `xzr`/`wzr`. No register is hard-wired to an operation like `rax` is on x86.

---

## 6.2 Register Sizes (`rax`, `eax`, `ax`, `al`)

For historical reasons each register can be accessed in several sizes. For `rax`:

```
 63                              32 31              16 15     8 7      0
┌──────────────────────────────────┬──────────────────┬────────┬────────┐
│                                  │                  │   ah   │   al   │
│                                  │                  ├────────┴────────┤
│                                  │                  │       ax        │
│                                  ├──────────────────┴─────────────────┤
│                                  │                eax                 │
├──────────────────────────────────┴────────────────────────────────────┤
│                                 rax                                   │
└───────────────────────────────────────────────────────────────────────┘
```

| 64-bit | 32-bit | 16-bit | 8-bit low | 8-bit high |
|--------|--------|--------|-----------|------------|
| `rax` | `eax` | `ax` | `al` | `ah` |
| `rbx` | `ebx` | `bx` | `bl` | `bh` |
| `rcx` | `ecx` | `cx` | `cl` | `ch` |
| `rdx` | `edx` | `dx` | `dl` | `dh` |
| `rsi` | `esi` | `si` | `sil` | n/a |
| `rdi` | `edi` | `di` | `dil` | n/a |
| `rbp` | `ebp` | `bp` | `bpl` | n/a |
| `rsp` | `esp` | `sp` | `spl` | n/a |
| `r8` | `r8d` | `r8w` | `r8b` | n/a |
| ... | ... | ... | ... | ... |
| `r15` | `r15d` | `r15w` | `r15b` | n/a |

### The crucial write rules

How a write affects the *rest* of the 64-bit register depends on the size:

| You write to... | Effect on the upper bits |
|-----------------|--------------------------|
| 64-bit register (`rax`) | Whole register replaced |
| **32-bit register (`eax`)** | **Upper 32 bits are cleared to zero** |
| 16-bit register (`ax`) | Upper 48 bits are **kept** |
| 8-bit register (`al`, `ah`) | All other bits are **kept** |

The 32-bit rule is why `mov eax, 1` can replace `mov rax, 1`: it is shorter and gives the same result. It is also why `xor eax, eax` is the standard way to zero all of `rax`.

```nasm
; file: subregs.asm
global _start

section .text
_start:
    mov rax, 0x1122334455667788
    mov al, 0xFF            ; only the low byte changes:    0x11223344556677FF
    shr rax, 8              ; shift right by one byte:      0x0011223344556677
    mov rdi, rax
    and rdi, 0xFF           ; keep the low byte:            0x77 = 119
    mov rax, 60
    syscall
```

More examples, with the resulting value of `rax` after each line, starting from `rax = 0x1122334455667788`:

```nasm
mov eax, 0xAABBCCDD     ; rax = 0x00000000AABBCCDD  (upper half cleared!)
mov ax,  0xAABB         ; rax = 0x112233445566AABB  (upper 48 bits kept)
mov ah,  0xAA           ; rax = 0x112233445566AA88  (only bits 8-15 change)
mov al,  0xAA           ; rax = 0x11223344556677AA  (only bits 0-7 change)
```

### Restrictions

- `ah`, `bh`, `ch`, `dh` **cannot** be used in an instruction that also needs a REX prefix (such as one using `r8`-`r15`, `sil`, or `dil`). The assembler reports an error.
- Mixing sizes in one instruction is an error: `mov eax, bl` is invalid. Use `movzx eax, bl` to widen.

---

## 6.3 The Instruction Pointer and Flags Register

### `rip`: the instruction pointer

`rip` holds the address of the **next instruction to be fetched**. You cannot write to it with `mov`; it changes through `jmp`, `call`, `ret`, and conditional jumps. You can *read* it indirectly through **RIP-relative addressing**, which is how position-independent code finds its data:

```nasm
lea rax, [rel message]      ; rax = address of message, computed relative to rip
```

### `rflags`: the flags register

`rflags` (its low 32 bits are called `eflags`) is a collection of single-bit **flags**. The ALU updates the status flags after most arithmetic and logic instructions, and conditional jumps read them (Lessons 11 and 12).

| Bit | Name | Symbol | Meaning |
|-----|------|--------|---------|
| 0 | Carry | `CF` | Unsigned overflow (carry/borrow out of the top bit) |
| 2 | Parity | `PF` | Low byte of result has an even number of 1 bits |
| 4 | Auxiliary carry | `AF` | Carry out of bit 3 (used by BCD arithmetic) |
| 6 | Zero | `ZF` | Result was zero |
| 7 | Sign | `SF` | Result's top bit is 1 (negative if signed) |
| 8 | Trap | `TF` | Single-step mode (debuggers use this) |
| 9 | Interrupt enable | `IF` | CPU responds to hardware interrupts |
| 10 | Direction | `DF` | String instructions move backward when set |
| 11 | Overflow | `OF` | Signed overflow |

`CF`, `PF`, `AF`, `ZF`, `SF`, and `OF` are the **status flags**. `DF` is a **control flag**. `TF` and `IF` are **system flags**.

Flags can be read in a debugger:

```
(gdb) info registers eflags
eflags         0x246               [ PF ZF IF ]
```

---

## 6.4 Segment and Special Registers

### Segment registers

In 64-bit mode memory is "flat": one big address space. The six **segment registers** (`cs`, `ds`, `es`, `ss`, `fs`, `gs`) are mostly vestiges of the 16-bit and 32-bit eras. Two of them still matter to normal programs:

- `cs` holds the **code segment selector**, which tells the CPU it is running 64-bit code and at what privilege level.
- `fs` (on Linux) and `gs` (on Windows) point to **thread-local storage**. Compilers use them to find per-thread data, and stack protectors read the stack *canary* from `fs:0x28`.

### Vector registers

| Registers | Width | Introduced with |
|-----------|-------|-----------------|
| `mm0`-`mm7` | 64 bits | MMX (legacy) |
| `xmm0`-`xmm15` | 128 bits | SSE (floating point and SIMD, Lessons 26 and 27) |
| `ymm0`-`ymm15` | 256 bits | AVX |
| `zmm0`-`zmm31` | 512 bits | AVX-512 |

The `xmm` registers are also where floating-point arguments and return values go in the System V calling convention.

### System registers

Normal programs never touch these, but they appear when reading kernel or bootloader code:

| Registers | Purpose |
|-----------|---------|
| `cr0`, `cr2`, `cr3`, `cr4` | Control registers: paging, protection, page-table base |
| `dr0`-`dr7` | Debug registers: hardware breakpoints |
| `gdtr`, `idtr` | Descriptor table locations (Lesson 32) |
| MSRs | Model-specific registers, read with `rdmsr` |

These can only be accessed in **ring 0** (kernel mode). Attempting to read `cr0` in a normal program causes a general protection fault.

---

[Previous](./[5]-How-a-CPU-Works.md) | [Table of Contents](./[0]-Introduction-to-Assembly.md) | [Next](./[7]-Syntax-and-Structure.md)
