[Previous](./[0]-Introduction-to-Assembly.md) | [Table of Contents](./[0]-Introduction-to-Assembly.md) | [Next](./[2]-Assembling-and-Linking.md)

*Getting Started*

# Lesson 1 - Installing an Assembler (NASM, GAS, MASM)

Before you can write a single line of Assembly you need the tools that turn it into something a CPU can run. This lesson explains what an assembler is, helps you install one on your platform, and shows how to confirm everything works.

This course uses **NASM** on **Linux** as its primary toolchain. If you use Windows, the easiest path is **WSL** (Windows Subsystem for Linux), which gives you a real Linux environment. The MASM and GAS sections are included so you can recognise the other major assemblers when you meet them.

---

## 1.1 What Is an Assembler?

A CPU only understands **machine code**: raw numbers (bytes) that encode instructions. Humans write **assembly**, a readable text form where each line is one instruction:

```nasm
mov eax, 1      ; human-readable assembly
```

```
B8 01 00 00 00  ; the same instruction as machine code (hex bytes)
```

An **assembler** is the program that converts assembly text into machine code. It is much simpler than a compiler: it does not optimise or restructure your code, it translates each line almost one-to-one and resolves names (labels) into addresses.

Different assemblers exist because there is no single standard assembly syntax:

| Assembler | Full name | Syntax | Typical platform |
|-----------|-----------|--------|------------------|
| **NASM** | Netwide Assembler | Intel | Linux, macOS, Windows |
| **GAS** (`as`) | GNU Assembler | AT&T by default (Intel optional) | Linux, macOS, bare-metal |
| **MASM** | Microsoft Macro Assembler | Intel | Windows |
| **FASM**, **YASM** | Flat / Yet Another Assembler | Intel | Cross-platform |

The assembler produces an **object file**. A separate tool, the **linker**, combines object files into the final executable. That second step is covered in Lesson 2.

> **ARM64 note:** assembling for ARM64 uses a different assembler (for example `aarch64-linux-gnu-as`, or GAS on a native ARM machine). The workflow (assemble, then link) is identical.

---

## 1.2 Installing NASM (Linux / macOS)

### Linux

Use your distribution's package manager.

```bash
# Debian / Ubuntu / Linux Mint / WSL
sudo apt update
sudo apt install nasm

# Fedora / RHEL
sudo dnf install nasm

# Arch / Manjaro
sudo pacman -S nasm
```

You can also download release builds from [https://www.nasm.us/pub/nasm/releasebuilds/](https://www.nasm.us/pub/nasm/releasebuilds/) and build from source:

```bash
tar xf nasm-X.YY.ZZ.tar.xz
cd nasm-X.YY.ZZ
./configure
make
sudo make install
```

### macOS

Install it with [Homebrew](https://brew.sh):

```bash
brew install nasm
```

> **Important:** Apple's own `nasm` in `/usr/bin` is very old. After installing with Homebrew, make sure `which nasm` points to the Homebrew version.

The code in this course uses **Linux system calls**, which differ from macOS system calls. On macOS the object format is `macho64`, system call numbers are offset by `0x2000000`, and Apple Silicon Macs can only run x86-64 programs through Rosetta. For following this course, a Linux virtual machine or a container is strongly recommended.

### Windows (via WSL)

Open PowerShell as administrator and run:

```powershell
wsl --install
```

Restart, open the Ubuntu terminal, and follow the Linux instructions above.

---

## 1.3 Installing GAS and the GNU Toolchain

GAS (`as`) is the assembler that GCC uses internally. It is part of **GNU Binutils**, which also contains the linker (`ld`), the disassembler (`objdump`), and other tools you will use throughout this course.

```bash
# Debian / Ubuntu / WSL: installs gcc, make, as, ld, objdump, ...
sudo apt install build-essential

# Fedora / RHEL
sudo dnf groupinstall "Development Tools"

# Arch / Manjaro
sudo pacman -S base-devel
```

You will also want the debugger, which is used from Lesson 3 onward:

```bash
sudo apt install gdb
```

GAS uses **AT&T syntax** by default, which looks different from NASM's Intel syntax:

| | NASM (Intel) | GAS (AT&T) |
|---|---|---|
| Operand order | `mov rax, 5` (destination first) | `movq $5, %rax` (source first) |
| Registers | `rax` | `%rax` |
| Immediates | `5` | `$5` |
| Size | `mov dword [x], 1` | `movl $1, x` |

Lesson 7 compares the two syntaxes in detail. A minimal GAS program looks like this:

```gas
# file: exit_gas.s
.globl _start
.text
_start:
    movq $60, %rax      # syscall number for exit
    movq $7, %rdi       # exit status
    syscall
```

Assemble and link it:

```bash
as exit_gas.s -o exit_gas.o
ld exit_gas.o -o exit_gas
./exit_gas
echo $?          # prints 7
```

---

## 1.4 Installing MASM (Windows)

MASM ships with **Visual Studio** (or the lighter **Build Tools for Visual Studio**). The 64-bit assembler is called `ml64.exe`.

1. Download *Build Tools for Visual Studio* (or Visual Studio Community) from Microsoft.
2. In the installer select the **Desktop development with C++** workload. This includes MASM, the linker, and the Windows SDK libraries.
3. Open the **x64 Native Tools Command Prompt for VS** from the Start menu. A normal command prompt does not have `ml64` on its `PATH`.

A minimal 64-bit MASM program that exits with status 7:

```masm
; file: exit_masm.asm
extrn ExitProcess : PROC

.code
main PROC
    sub rsp, 28h        ; shadow space + alignment (see Lesson 17)
    mov ecx, 7          ; first argument (Microsoft x64 convention)
    call ExitProcess
main ENDP
END
```

Build and run it:

```bat
ml64 exit_masm.asm /link /subsystem:console /entry:main kernel32.lib
exit_masm.exe
echo %ERRORLEVEL%
```

Windows programs call the Windows API (`ExitProcess`) rather than Linux system calls, so the examples in later lessons need small changes to run on Windows. NASM can also target Windows (`-f win64`), so you can keep using it there.

---

## 1.5 Verifying Your Installation

Check that each tool is installed and reachable:

```bash
nasm -v               # NASM version 2.xx.xx
ld --version          # GNU ld (GNU Binutils) 2.xx
gcc --version
gdb --version
objdump --version
make --version
```

Every command should print a version number. If you see `command not found`, the tool is not installed or not on your `PATH`.

Now do a complete end-to-end test. Create a file named `test.asm`:

```nasm
; file: test.asm
global _start

section .text
_start:
    mov rax, 60         ; syscall: exit
    mov rdi, 42         ; status code
    syscall
```

Assemble, link, run, and inspect the exit status:

```bash
nasm -f elf64 test.asm -o test.o
ld test.o -o test
./test
echo $?
```

Expected output:

```
42
```

If you see `42`, your assembler, linker, and operating system are all working together. Each step is explained in Lesson 2.

Common problems:

| Symptom | Likely cause |
|---------|--------------|
| `nasm: command not found` | NASM is not installed or not on `PATH` |
| `ld: cannot find entry symbol _start` | Missing `global _start` or a typo in the label |
| `Exec format error` | You built a 32-bit or macOS binary, or are on an ARM machine without emulation |
| `echo $?` prints `0` | The program exited with a different status than you expected: check `mov rdi, ...` |

---

[Previous](./[0]-Introduction-to-Assembly.md) | [Table of Contents](./[0]-Introduction-to-Assembly.md) | [Next](./[2]-Assembling-and-Linking.md)
