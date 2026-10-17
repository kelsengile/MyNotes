[Previous](./[7]-Syntax-and-Structure.md) | [Table of Contents](./[0]-Introduction-to-Assembly.md) | [Next](./[9]-Arithmetic.md)

*Foundations*

# Lesson 8 - Data & Memory Addressing (`mov`, `lea`, addressing modes)

Programs need to store data and move it around. This lesson covers how to define data, how `mov` works, all the ways an instruction can name a memory location (addressing modes), and the special `lea` instruction.

---

## 8.1 Defining Data (`db`, `dw`, `dd`, `dq`)

Data directives place bytes into the current section and attach a label to them.

| Directive | Size | Example |
|-----------|------|---------|
| `db` | 1 byte | `flag db 1` |
| `dw` | 2 bytes | `year dw 2025` |
| `dd` | 4 bytes | `count dd 100000` |
| `dq` | 8 bytes | `big dq 0x123456789ABCDEF0` |

You can give several values at once to make an **array**, and strings are just byte sequences:

```nasm
section .data
byte_val   db 0xFF                  ; one byte
numbers    dd 10, 20, 30, 40        ; four dwords = 16 bytes
greeting   db "Hello", 0            ; 5 characters plus a NUL terminator
newline    db 10                    ; a single newline character
mixed      db "Hi", 10, 0           ; 'H', 'i', 0x0A, 0x00
pi_approx  dq 3                     ; one qword
zeros      times 8 db 0             ; eight zero bytes
```

Strings in NASM are **not** automatically NUL-terminated. Add the `0` yourself if you want a C-style string.

### Reserving uninitialised space

In `.bss` use the `res` directives, which take a **count** of items:

| Directive | Reserves |
|-----------|----------|
| `resb n` | n bytes |
| `resw n` | n words (2 bytes each) |
| `resd n` | n dwords (4 bytes each) |
| `resq n` | n qwords (8 bytes each) |

```nasm
section .bss
buffer  resb 256        ; 256 bytes
table   resd 100        ; 100 dwords = 400 bytes
result  resq 1          ; one qword
```

### Constants with `equ`

```nasm
BUFFER_SIZE equ 256
STDOUT      equ 1
```

An `equ` is a name for a number resolved at assembly time. It takes no memory and has no address, so you must **not** put brackets around it.

---

## 8.2 The `mov` Instruction

`mov destination, source` copies a value. It does not change the source and does **not** affect the flags.

| Form | Example | Notes |
|------|---------|-------|
| register ← immediate | `mov rax, 10` | |
| register ← register | `mov rax, rbx` | Sizes must match |
| register ← memory | `mov eax, [count]` | Size taken from the register |
| memory ← register | `mov [count], eax` | |
| memory ← immediate | `mov dword [count], 5` | **Size keyword required** |
| memory ← memory | *not allowed* | Use a register in between |

### Size specifiers

When no register tells the assembler the size, use `byte`, `word`, `dword`, or `qword`:

```nasm
mov byte  [flag], 1         ; writes 1 byte
mov word  [year], 2025      ; writes 2 bytes
mov dword [count], 5        ; writes 4 bytes
mov qword [big], 5          ; writes 8 bytes
```

Forgetting the size gives the error `operation size not specified`.

### Moving between sizes

```nasm
movzx eax, byte [flag]      ; zero-extend 8 -> 32 bits
movzx rax, word [year]      ; zero-extend 16 -> 64 bits
movsx eax, byte [delta]     ; sign-extend 8 -> 32 bits
movsxd rax, dword [count]   ; sign-extend 32 -> 64 bits
```

Remember that a `mov` into a 32-bit register zero-extends into the full 64-bit register, so no special instruction is needed to zero-extend a 32-bit value.

### Exchanging

```nasm
xchg rax, rbx               ; swap two registers
xchg [count], eax           ; swap register and memory (implicitly locked!)
```

`xchg` with a memory operand is **atomic** and slow. Prefer three `mov`s through a register for ordinary swaps.

### Immediate sizes

`mov rax, imm` can load any 64-bit constant. Most other instructions only accept a **32-bit** immediate (sign-extended to 64 bits). To use a big constant in an `add`, first `mov` it into a register.

```nasm
mov rax, 0x123456789ABCDEF0     ; ok: the only instruction with a 64-bit immediate
add rbx, rax                    ; add rbx, 0x123456789ABCDEF0 would be an error
```

---

## 8.3 Addressing Modes

An **addressing mode** is the way an instruction specifies *where its operand is*. x86-64 offers several. The general form of a memory operand is:

```
[ base + index*scale + displacement ]
```

- **base:** any 64-bit register
- **index:** any 64-bit register except `rsp`
- **scale:** `1`, `2`, `4`, or `8` (the size of one array element)
- **displacement:** a constant

All parts are optional, but at least one must be present.

| Mode | Example | Meaning |
|------|---------|---------|
| **Immediate** | `mov rax, 5` | The value is part of the instruction |
| **Register** | `mov rax, rbx` | The value is in a register |
| **Direct** (absolute) | `mov eax, [count]` | Address is a constant (a label) |
| **Register indirect** | `mov eax, [rbx]` | Address is in a register |
| **Base + displacement** | `mov eax, [rbx + 8]` | Address is register plus constant |
| **Base + index** | `mov eax, [rbx + rcx]` | Two registers added |
| **Scaled index** | `mov eax, [rbx + rcx*4]` | Array indexing |
| **Full** | `mov eax, [rbx + rcx*4 + 8]` | Base, scaled index, and displacement |
| **RIP-relative** | `mov eax, [rel count]` | Address relative to `rip` |

### Arrays

The scale factor matches the element size, so the CPU does the multiplication for you:

```nasm
section .data
numbers dd 10, 20, 30, 40       ; 4-byte elements

section .text
    mov esi, 2                  ; index 2
    mov eax, [numbers + rsi*4]  ; eax = numbers[2] = 30
```

If the elements are 8 bytes use `*8`; for bytes, no scale is needed. Scale values like 3 or 16 are not possible, so use `lea` or `imul` first for other sizes.

### RIP-relative addressing

Position-independent executables (the default for `gcc`) cannot use absolute addresses. In 64-bit mode you can address data relative to `rip`. In NASM:

```nasm
default rel                 ; make [label] RIP-relative by default
mov eax, [count]            ; now assembled as [rip + offset]
mov eax, [rel count]        ; explicit form without 'default rel'
```

When linking with `ld` into a static executable (as most lessons do), absolute addressing is fine. Lesson 23 explains when RIP-relative addressing becomes necessary.

### Label arithmetic

Labels are numbers, so you can add to them:

```nasm
mov eax, [numbers + 4]          ; the second element (index 1)
mov eax, [numbers + 12]         ; the fourth element
```

---

## 8.4 `lea` and Effective Addresses

`lea` stands for **Load Effective Address**. It computes the address expression in the brackets **but does not access memory**. It stores the address itself in the destination.

```nasm
mov rax, [rbx + 8]      ; rax = the 8 bytes stored AT address rbx+8
lea rax, [rbx + 8]      ; rax = rbx + 8      (no memory access at all)
```

Typical uses:

**1. Getting a pointer:**

```nasm
lea rsi, [numbers]          ; rsi = address of numbers
lea rsi, [rel numbers]      ; same, RIP-relative (PIE friendly)
```

**2. Pointer to an array element:**

```nasm
lea rdi, [numbers + rcx*4]  ; rdi = &numbers[rcx]
```

**3. Fast arithmetic.** Because `lea` can add two registers, scale one, and add a constant in a single instruction, compilers use it as a three-operand adder. It also **does not change the flags**.

```nasm
lea rax, [rdi + rsi]        ; rax = rdi + rsi          (add with separate destination)
lea rax, [rdi + 1]          ; rax = rdi + 1
lea rax, [rbx + rbx*4]      ; rax = rbx * 5
lea rax, [rbx*8 + 3]        ; rax = rbx * 8 + 3
```

`mov rsi, label` and `lea rsi, [label]` both give you the address. The `mov` form is a 64-bit immediate move; the `lea [rel label]` form is shorter and works in position-independent code.

---

## 8.5 Reading and Writing Memory

Here is a complete program that reads array elements using different addressing modes, computes a sum, writes the result back into the array, and exits with it:

```nasm
; file: memory.asm
global _start

section .data
numbers dd 10, 20, 30, 40           ; four 4-byte integers

section .text
_start:
    mov eax, [numbers]              ; direct:         eax = 10
    mov ebx, [numbers + 4]          ; label + offset: ebx = 20

    mov rsi, 2
    mov ecx, [numbers + rsi*4]      ; scaled index:   ecx = 30

    lea rdx, [numbers]              ; rdx = address of the array
    mov r8d, [rdx + 12]             ; base + offset:  r8d = 40 (not used below)

    add eax, ebx                    ; 30
    add eax, ecx                    ; 60

    mov [numbers + 12], eax         ; write: numbers[3] = 60
    mov edi, [numbers + 12]         ; read it back
    mov eax, 60                     ; syscall: exit
    syscall                         ; exit status = 60
```

The array lives in `.data`, which is writable, so the store works. If `numbers` were in `.rodata` the `mov [numbers + 12], eax` would crash with a segmentation fault.

### Little-endian memory in practice

Writing a dword and reading it back as bytes shows endianness again:

```nasm
; file: bytes.asm
global _start

section .bss
word_buf resd 1

section .text
_start:
    mov dword [word_buf], 0x01020304
    movzx edi, byte [word_buf]      ; lowest-address byte = 0x04
    mov eax, 60
    syscall                         ; exit status 4
```

### Accessing memory safely

- **Know the size.** `mov eax, [x]` reads 4 bytes, `mov al, [x]` reads 1 byte. Reading a variable with the wrong size is a classic bug.
- **Stay in bounds.** The CPU does not know where your array ends. Reading or writing past it silently corrupts neighbouring data (or crashes).
- **Check alignment for performance.** Aligned data (a dword at an address divisible by 4) is faster, and some SIMD instructions require 16-byte alignment (Lesson 27).
- **Brackets matter.** `mov rax, label` is an address, `mov rax, [label]` is a value.

---

[Previous](./[7]-Syntax-and-Structure.md) | [Table of Contents](./[0]-Introduction-to-Assembly.md) | [Next](./[9]-Arithmetic.md)
