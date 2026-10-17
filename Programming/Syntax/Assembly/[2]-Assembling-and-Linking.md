[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-Assembly.md) | [Next](./[3]-Development-Environment.md)

*Getting Started*

# Lesson 2 - Assembling, Linking & Running

This lesson walks through the complete journey from a text file to a running program, introduces the `nasm` and `ld` commands in detail, shows how to link with `gcc`, and finishes with a `Makefile` so you never have to type the commands again.

---

## 2.1 From Source Code to Executable

Turning assembly into a running program takes four steps:

```
 source.asm  ──►  source.o  ──►  program  ──►  running process
   (text)      assembler     linker       operating system
                (nasm)        (ld/gcc)       (loader)
```

1. **Write** the source file (`.asm`), plain text.
2. **Assemble:** the assembler translates instructions into machine code and writes an **object file** (`.o`). The object file contains your code and data, plus a **symbol table** listing names it defines and names it needs from elsewhere. Addresses are not final yet.
3. **Link:** the linker joins object files (and libraries), assigns final addresses, resolves symbols, and writes an **executable** in the **ELF** format on Linux.
4. **Run:** the operating system's **loader** maps the executable into memory, sets up a stack, and jumps to the **entry point**.

The entry point on Linux, when you link with `ld`, is a symbol named `_start`. When you link with `gcc`, the C runtime provides `_start` and it calls your `main` function.

> **ARM64 note:** the pipeline is identical. Only the assembler/linker names (and the instructions) change.

---

## 2.2 Assembling with `nasm`

The basic form:

```bash
nasm -f elf64 program.asm -o program.o
```

| Option | Meaning |
|--------|---------|
| `-f elf64` | Output format: 64-bit ELF (Linux). Others: `elf32`, `macho64`, `win64`, `bin` |
| `-o file` | Name of the output file |
| `-g` | Include debug information |
| `-F dwarf` | Debug format to use with `-g` (DWARF is what GDB understands) |
| `-l file.lst` | Write a **listing file** showing addresses and the machine code bytes |
| `-Wall` | Enable all warnings |
| `-d NAME=value` | Define a macro from the command line |

A listing file is a great learning tool. Given this program:

```nasm
; file: add.asm
global _start

section .text
_start:
    mov rax, 5
    add rax, 3
    mov rdi, rax
    mov rax, 60
    syscall
```

Running `nasm -f elf64 -l add.lst add.asm -o add.o` produces a listing similar to:

```
     1                                  global _start
     2
     3                                  section .text
     4                                  _start:
     5 00000000 B805000000                  mov rax, 5
     6 00000005 4883C003                    add rax, 3
     7 00000009 4889C7                      mov rdi, rax
     8 0000000C B83C000000                  mov rax, 60
     9 00000011 0F05                        syscall
```

Each line shows the **offset** within the section, the **machine code bytes**, and your original source. Lesson 5 explains how those bytes are formed.

---

## 2.3 Linking with `ld` and `gcc`

### Linking with `ld`

For programs that use only Linux system calls and have their own `_start`:

```bash
ld program.o -o program
```

Useful options:

| Option | Meaning |
|--------|---------|
| `-o file` | Output file name |
| `-e symbol` | Use another entry symbol instead of `_start` |
| `-m elf_i386` | Link a 32-bit program |
| `-s` | Strip symbols (smaller file, harder to debug) |
| `-N` | Make all sections writable |

Because `ld` links no libraries, the result is tiny. A program like the one above is only a few hundred bytes of code.

### Linking with `gcc`

When your assembly calls C library functions (`printf`, `malloc`, ...) or provides a function named `main`, link with `gcc` instead:

```bash
gcc program.o -o program
```

Here the C runtime (`crt1.o` and others) supplies `_start`, which initialises the C library, calls `main`, and calls `exit` with `main`'s return value. On modern distributions `gcc` produces **position-independent executables (PIE)** by default. Lesson 23 shows how to write PIE-friendly code; for now, you can also pass `-no-pie` to get a traditional fixed-address executable:

```bash
gcc -no-pie program.o -o program
```

### Choosing between them

| You want... | Entry point | Link with |
|-------------|-------------|-----------|
| Pure assembly, Linux system calls only | `_start` | `ld` |
| To call `printf`, `malloc`, etc. | `main` | `gcc` |
| To mix assembly and C source files | `main` in C | `gcc` |

---

## 2.4 Your First Program: Exit with a Status Code

Every process ends with an **exit status**, a number from 0 to 255 that the shell can read. By convention `0` means success and anything else means failure. The simplest useful program does nothing except exit with a chosen status.

```nasm
; file: exit.asm
global _start               ; make _start visible to the linker

section .text               ; code lives in the .text section
_start:                     ; the entry point
    mov rax, 60             ; system call number 60 = exit
    mov rdi, 42             ; first argument: the exit status
    syscall                 ; ask the kernel to run the system call
```

Line by line:

- `global _start` exports the label `_start` so the linker can find the entry point.
- `section .text` starts the section that holds executable code.
- `mov rax, 60` places the number of the `exit` system call in register `rax`. Linux reads the system call number from `rax`.
- `mov rdi, 42` places the first argument (the status) in `rdi`.
- `syscall` transfers control to the kernel, which ends the process.

Build and run:

```bash
nasm -f elf64 exit.asm -o exit.o
ld exit.o -o exit
./exit
echo $?
```

Output:

```
42
```

`$?` is a shell variable that holds the exit status of the last command. Without the final `syscall`, the CPU would keep executing whatever bytes follow your code and the program would crash with a **segmentation fault**. In Assembly, nothing returns to the operating system for you.

Lesson 19 covers system calls in depth.

---

## 2.5 Introduction to Makefiles

Typing the same `nasm` and `ld` commands repeatedly is tedious. **Make** reads a file named `Makefile` and runs only the commands needed to rebuild what changed.

A rule has this shape:

```
target: dependencies
<TAB>command
```

> **Warning:** the command lines must start with a real **TAB** character, not spaces. This is the most common Makefile error (`missing separator`).

A reusable Makefile for a single-file program such as `exit.asm`:

```makefile
# file: Makefile
AS      = nasm
ASFLAGS = -f elf64 -g -F dwarf
LD      = ld

TARGET  = exit
OBJ     = $(TARGET).o

all: $(TARGET)

$(TARGET): $(OBJ)
	$(LD) -o $@ $<

%.o: %.asm
	$(AS) $(ASFLAGS) $< -o $@

clean:
	rm -f $(TARGET) $(OBJ)

.PHONY: all clean
```

What the special symbols mean:

| Symbol | Meaning |
|--------|---------|
| `$@` | The target of the rule (the file being built) |
| `$<` | The first dependency |
| `$^` | All dependencies |
| `%.o: %.asm` | A **pattern rule**: how to build any `.o` from the matching `.asm` |
| `.PHONY` | Names that are commands, not files |

Use it like this:

```bash
make          # builds ./exit
make clean    # removes build products
```

For a program that links with `gcc`, change the link rule:

```makefile
# file: Makefile.gcc
AS      = nasm
ASFLAGS = -f elf64 -g -F dwarf
CC      = gcc

TARGET  = hello
OBJ     = $(TARGET).o

all: $(TARGET)

$(TARGET): $(OBJ)
	$(CC) -no-pie -o $@ $<

%.o: %.asm
	$(AS) $(ASFLAGS) $< -o $@

clean:
	rm -f $(TARGET) $(OBJ)

.PHONY: all clean
```

---

[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-Assembly.md) | [Next](./[3]-Development-Environment.md)
