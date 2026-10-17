[Previous](./[2]-Assembling-and-Linking.md) | [Table of Contents](./[0]-Introduction-to-Assembly.md) | [Next](./[4]-Number-Systems.md)

*Getting Started*

# Lesson 3 - Your Development Environment (editors, debuggers, disassemblers)

Writing assembly is only half the job. The other half is **seeing what your program is doing**. This lesson sets up an editor, introduces the GDB debugger (and its friendly extensions), shows how to disassemble binaries with `objdump`, and recommends a project layout.

---

## 3.1 Choosing an Editor

Assembly is plain text, so any editor works. What helps is **syntax highlighting**, **consistent indentation**, and a **built-in terminal** so you can assemble and run quickly.

| Editor | Notes |
|--------|-------|
| **VS Code** | Free. Install an extension such as *x86 and x86_64 Assembly* for NASM highlighting. Integrated terminal and debugger front-ends. |
| **Vim / Neovim** | Ships with `nasm` syntax highlighting (`:set ft=nasm`). Fast over SSH. |
| **Emacs** | `nasm-mode` package. |
| **Sublime Text / Notepad++** | Highlighting for NASM available built-in or via packages. |
| **CLion / Kate / Geany** | Also work well with `.asm` files. |

Habits that make assembly easier to read:

- Use **spaces or tabs consistently**. A common style is: labels at column 0, instructions indented by 4 spaces, comments aligned to the right.
- Write a **comment on almost every line** while learning. Assembly has no names for the things you are doing; comments are the only documentation.
- Use a **monospace font** and a visible **column ruler** at 80 characters.

```nasm
; file: style.asm
global _start

section .text
_start:                     ; labels in column 0
    mov rax, 10             ; instructions indented
    add rax, 5              ; comments aligned
    mov rdi, rax
    mov rax, 60
    syscall
```

---

## 3.2 Debugging with GDB (and GEF/pwndbg)

**GDB** (the GNU Debugger) lets you pause a program, step through it **one instruction at a time**, and inspect registers and memory. Build with debug info (`-g -F dwarf`) so GDB can show your source lines and labels.

Use this program as a target. It adds two numbers and exits with the result:

```nasm
; file: debug_me.asm
global _start

section .text
_start:
    mov rax, 5
    add rax, 3
    mov rdi, rax
    mov rax, 60
    syscall
```

```bash
nasm -f elf64 -g -F dwarf debug_me.asm -o debug_me.o
ld debug_me.o -o debug_me
gdb ./debug_me
```

A typical session (the `(gdb)` text is GDB's prompt; you type what follows it):

```
(gdb) set disassembly-flavor intel
(gdb) break _start
Breakpoint 1 at 0x401000: file debug_me.asm, line 6.
(gdb) run
Breakpoint 1, _start () at debug_me.asm:6
(gdb) stepi
(gdb) info registers rax
rax            0x5                 5
(gdb) stepi
(gdb) info registers rax
rax            0x8                 8
(gdb) continue
[Inferior 1 (process N) exited with code 010]
```

GDB prints exit codes in **octal**, so `010` is 8.

Essential commands:

| Command | Short | Action |
|---------|-------|--------|
| `break _start` / `break *0x401000` | `b` | Set a breakpoint on a label or address |
| `run` | `r` | Start the program |
| `stepi` | `si` | Execute **one instruction** (steps into calls) |
| `nexti` | `ni` | One instruction, stepping **over** calls |
| `continue` | `c` | Run until the next breakpoint |
| `info registers` | `i r` | Show all general-purpose registers |
| `info registers rax rdi` | | Show specific registers |
| `print $rax` / `p/x $rax` | `p` | Print a register (`/x` hex, `/d` decimal, `/t` binary) |
| `x/4gx $rsp` | | Examine 4 giant (8-byte) words at `rsp`, in hex |
| `x/s address` | | Show a string at an address |
| `x/5i $rip` | | Disassemble 5 instructions starting at `rip` |
| `disassemble` | `disas` | Disassemble the current function |
| `layout regs` | | Text UI showing registers and code (`Ctrl+X A` exits) |
| `display/i $rip` | | Show the next instruction after every step |
| `set disassembly-flavor intel` | | Use Intel syntax (the default is AT&T) |
| `quit` | `q` | Exit GDB |

The `x` command has the form `x/NFU address`: **N** is the count, **F** is the format (`x` hex, `d` decimal, `s` string, `i` instruction), and **U** is the unit size (`b` byte, `h` 2 bytes, `w` 4 bytes, `g` 8 bytes).

To make Intel syntax permanent, add this line to `~/.gdbinit`:

```
set disassembly-flavor intel
```

### GEF and pwndbg

Plain GDB shows little context. **GEF** and **pwndbg** are plugins that automatically display registers, the stack, and the code around `rip` every time the program stops. They are very popular for reverse engineering and security work (Lessons 33 to 36). You only need one of them.

```bash
# GEF: a single-file plugin (see the project page for the current installer)
bash -c "$(curl -fsSL https://gef.blah.cat/sh)"

# pwndbg
git clone https://github.com/pwndbg/pwndbg
cd pwndbg
./setup.sh
```

Always check each project's README for the current installation instructions. Both are optional; everything in this course works in plain GDB.

---

## 3.3 Disassembling with `objdump`

A **disassembler** performs the reverse of an assembler: it reads machine code and prints assembly. `objdump` can disassemble any binary or object file, including programs you did not write.

```bash
objdump -d -M intel debug_me
```

Output:

```
debug_me:     file format elf64-x86-64

Disassembly of section .text:

0000000000401000 <_start>:
  401000:	b8 05 00 00 00       	mov    eax,0x5
  401005:	48 83 c0 03          	add    rax,0x3
  401009:	48 89 c7             	mov    rdi,rax
  40100c:	b8 3c 00 00 00       	mov    eax,0x3c
  401011:	0f 05                	syscall
```

Each line shows the **address**, the **machine code bytes**, and the **disassembled instruction**. Notice that `mov rax, 5` was assembled as `mov eax, 0x5`: writing a 32-bit register clears the upper half of the 64-bit register, so the shorter encoding gives the same result (Lesson 6).

Useful options:

| Option | Meaning |
|--------|---------|
| `-d` | Disassemble the executable sections |
| `-D` | Disassemble **all** sections (including data) |
| `-M intel` | Intel syntax (the default is AT&T) |
| `-S` | Intermix source code (needs debug info) |
| `-t` | Show the **symbol table** |
| `-h` | Show **section headers** |
| `-s -j .data` | Hex dump of a single section |
| `--no-show-raw-insn` | Hide the machine code bytes |

Related tools worth knowing:

| Tool | Purpose |
|------|---------|
| `readelf -h file` | ELF header, including the entry point address |
| `nm file` | List symbols |
| `xxd file` / `hexdump -C file` | Raw hex dump of any file |
| `file file` | Identify the file type |
| `strace ./program` | Trace system calls a program makes (very useful from Lesson 19) |

---

## 3.4 A Recommended Project Layout

Small experiments fit in one folder, but a layout like this scales to the multi-file projects later in the course:

```
asm-course/
├── src/            # .asm source files
│   ├── hello.asm
│   └── utils.asm
├── include/        # .inc files with macros and constants
│   └── syscalls.inc
├── build/          # object files and executables (generated)
├── Makefile
└── README.md
```

A `Makefile` for this layout that builds every `.asm` file in `src/` into its own program:

```makefile
# file: Makefile
AS      = nasm
ASFLAGS = -f elf64 -g -F dwarf -I include/
LD      = ld

SRC     = $(wildcard src/*.asm)
BIN     = $(patsubst src/%.asm,build/%,$(SRC))

all: $(BIN)

build/%: build/%.o
	$(LD) -o $@ $<

build/%.o: src/%.asm | build
	$(AS) $(ASFLAGS) $< -o $@

build:
	mkdir -p build

clean:
	rm -rf build

.PHONY: all clean
```

The `-I include/` option tells NASM where to look for files referenced by `%include` (Lesson 22). Keep the `build/` folder out of version control by adding it to `.gitignore`.

---

[Previous](./[2]-Assembling-and-Linking.md) | [Table of Contents](./[0]-Introduction-to-Assembly.md) | [Next](./[4]-Number-Systems.md)
