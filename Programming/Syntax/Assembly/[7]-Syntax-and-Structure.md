[Previous](./[6]-Registers.md) | [Table of Contents](./[0]-Introduction-to-Assembly.md) | [Next](./[8]-Data-and-Addressing.md)

*Foundations*

# Lesson 7 - Assembly Syntax & Program Structure (sections, labels, directives)

Before diving into instructions you need to know how an assembly source file is laid out. This lesson explains the anatomy of a line, the three main sections, labels and directives, and compares Intel syntax (used by NASM) with AT&T syntax (used by GAS by default).

---

## 7.1 Instructions, Operands, and Comments

An assembly line has up to four parts, all optional:

```
label:    mnemonic    operands          ; comment
```

```nasm
start:    mov    rax, 5        ; put 5 into rax
```

- **Label:** a name for an address. It ends with a colon (the colon is optional in NASM for most labels, but always use it).
- **Mnemonic:** the instruction name (`mov`, `add`, `jmp`). It is not case-sensitive, but **labels are case-sensitive**.
- **Operands:** the data the instruction works on, separated by commas.
- **Comment:** everything after a `;` to the end of the line.

### Operand order

In Intel syntax the **destination comes first**, then the source:

```nasm
mov rax, rbx        ; rax = rbx    (copy rbx INTO rax)
add rax, 5          ; rax = rax + 5
```

You can remember it as the assignment `rax = rbx` written left to right.

### Operand types

| Type | Example | Meaning |
|------|---------|---------|
| **Register** | `rax`, `cl` | Value held in a register |
| **Immediate** | `5`, `0xFF`, `'A'` | A constant encoded in the instruction |
| **Memory** | `[rbx]`, `[count]`, `[rsi+8]` | Value stored at an address (square brackets!) |

The square brackets mean "the contents at this address". Without brackets the label is just its address:

```nasm
mov rax, count      ; rax = the ADDRESS of count
mov rax, [count]    ; rax = the VALUE stored at count
```

This is the single most common beginner mistake, and Lesson 8 returns to it.

### Rules for operands

- At most **one operand can be a memory reference**: `mov [a], [b]` is illegal. Go through a register.
- The sizes of both operands must match, or one must be an immediate.
- When the size cannot be inferred (for example memory with an immediate), you must say it: `mov dword [count], 5`.

### Comments and line continuation

```nasm
; A whole-line comment
mov rax, 1          ; end-of-line comment
mov rax, \
    1               ; a backslash continues the line
```

---

## 7.2 Sections: `.text`, `.data`, `.bss`

A program is split into **sections**, each holding a different kind of content with different permissions. The `section` directive switches between them.

| Section | Contents | Permissions | Occupies space in the file? |
|---------|----------|-------------|------------------------------|
| `.text` | Machine code | read + execute | Yes |
| `.data` | **Initialised** writable data | read + write | Yes |
| `.rodata` | Read-only data (constants, string literals) | read | Yes |
| `.bss` | **Uninitialised** writable data (zero-filled at load) | read + write | **No**, only its size is recorded |

Why `.bss` exists: a 1 MB buffer in `.data` would make your executable 1 MB larger. In `.bss` you only record "reserve 1 MB", and the loader gives you zeroed memory.

A complete program using all three:

```nasm
; file: hello.asm
global _start

section .data
msg     db "Hello, Assembly!", 10   ; string followed by newline (10)
msg_len equ $ - msg                 ; length computed by the assembler

section .bss
buffer  resb 64                     ; reserve 64 uninitialised bytes

section .text
_start:
    mov rax, 1              ; syscall: write
    mov rdi, 1              ; file descriptor 1 = stdout
    mov rsi, msg            ; address of the data
    mov rdx, msg_len        ; number of bytes
    syscall

    mov rax, 60             ; syscall: exit
    xor edi, edi            ; status 0
    syscall
```

Running it:

```bash
nasm -f elf64 hello.asm -o hello.o
ld hello.o -o hello
./hello
```

```
Hello, Assembly!
```

Writing to stdout uses a system call, which Lesson 19 explains fully. For now, read it as "print this memory".

> **Tip:** if you accidentally put an instruction in `.data`, the program will crash with a segmentation fault at that instruction, because `.data` is not executable. Likewise, writing to a `.rodata` label crashes.

---

## 7.3 Labels and Directives

### Labels

A label marks a location. The assembler replaces it with an address.

```nasm
section .text
_start:                 ; a global label (code address)
    jmp .skip           ; .skip is a LOCAL label belonging to _start
    nop
.skip:
    mov rax, 60
    syscall
```

- **Global labels** (`_start`, `main`, `print_string`) are unique across the file.
- **Local labels** start with a dot (`.loop`). They belong to the most recent non-local label, so you can reuse `.loop` and `.done` inside many different procedures. Internally `.skip` above is `_start.skip`.

### Directives

A **directive** is an instruction *to the assembler*, not to the CPU. It produces no machine instruction (though some, like `db`, emit data).

| Directive | Purpose | Example |
|-----------|---------|---------|
| `global name` | Export a symbol so the linker can see it | `global _start` |
| `extern name` | Import a symbol defined elsewhere | `extern printf` |
| `section name` | Switch section | `section .text` |
| `db` `dw` `dd` `dq` | Define initialised data | `count dd 10` |
| `resb` `resw` `resd` `resq` | Reserve uninitialised space | `buf resb 128` |
| `equ` | Define a constant | `SIZE equ 64` |
| `times n` | Repeat an instruction or data | `times 16 db 0` |
| `bits 64` | Select 64-bit code generation | `bits 64` |
| `default rel` | Make memory references RIP-relative by default | `default rel` |
| `align n` | Pad to an `n`-byte boundary | `align 16` |
| `%define`, `%macro`, `%include` | Preprocessor directives (Lesson 22) | |

### Special symbols

| Symbol | Meaning |
|--------|---------|
| `$` | The address of the **current line** |
| `$$` | The address of the start of the **current section** |

Using `$` to compute a string length is idiomatic: the length is the current address minus the address where the string started.

```nasm
msg     db "Hello", 10
msg_len equ $ - msg         ; 6, computed at assembly time
```

`equ` constants are computed by the assembler and do **not** occupy memory. `msg_len` must be written directly after `msg` for this to work.

### Entry point and `global`

The linker looks for `_start`. If you forget `global _start`, it reports `ld: warning: cannot find entry symbol _start` and the program will not run correctly.

---

## 7.4 Intel vs AT&T Syntax

There are two notations for x86 assembly. They produce the **same machine code**.

| Feature | Intel (NASM, MASM) | AT&T (GAS default) |
|---------|--------------------|--------------------|
| Operand order | `mov dest, src` | `mov src, dest` |
| Registers | `rax` | `%rax` |
| Immediates | `5`, `0x10` | `$5`, `$0x10` |
| Memory | `[rbx + rcx*4 + 8]` | `8(%rbx,%rcx,4)` |
| Size | Operand keyword: `mov dword [x], 1` | Mnemonic suffix: `movl $1, x` |
| Comments | `;` | `#` |
| Directives | `db`, `dd`, `section` | `.byte`, `.long`, `.section` |

Suffixes in AT&T syntax: `b` = byte, `w` = word, `l` = 32-bit, `q` = 64-bit.

The same program in both syntaxes:

```nasm
; file: intel_version.asm
global _start
section .text
_start:
    mov rax, 5
    add rax, 7
    mov rdi, rax
    mov rax, 60
    syscall
```

```gas
# file: att_version.s
.globl _start
.text
_start:
    movq $5, %rax
    addq $7, %rax
    movq %rax, %rdi
    movq $60, %rax
    syscall
```

You can build the AT&T version with GAS:

```bash
as att_version.s -o att_version.o
ld att_version.o -o att_version
```

### Using Intel syntax in GAS and GDB

GAS accepts Intel syntax if you ask:

```gas
.intel_syntax noprefix
.globl _start
.text
_start:
    mov rax, 60
    mov rdi, 3
    syscall
```

And you can ask `objdump` and GDB for Intel output:

```bash
objdump -d -M intel program
```

```
(gdb) set disassembly-flavor intel
```

This course uses Intel syntax throughout. You will still meet AT&T syntax in compiler output (`gcc -S` defaults to it, Lesson 24) and in Linux kernel sources, so learn to recognise it.

---

[Previous](./[6]-Registers.md) | [Table of Contents](./[0]-Introduction-to-Assembly.md) | [Next](./[8]-Data-and-Addressing.md)
