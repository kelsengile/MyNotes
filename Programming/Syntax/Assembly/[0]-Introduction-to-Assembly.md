[⬅ Back to README](../../../README.md)

<div align="center">
  <img src="https://api.iconify.design/vscode-icons/file-type-assembly.svg" width="50"/><br>
  <h1 style="margin-top: 0;">Assembly</h1>
</div>

Welcome! This is a self-paced course for learning Assembly, the human-readable form of machine code. It is the lowest level at which people routinely write programs, and it is the key to understanding how CPUs, compilers, operating systems, and security tools really work.

Download: [https://www.nasm.us/pub/nasm/releasebuilds/](https://www.nasm.us/pub/nasm/releasebuilds/)

> This course uses **x86-64** with the **NASM** assembler on Linux as its primary target, with notes on ARM64 where the ideas differ.

---

## What is Assembly?

Assembly is a thin text layer over a processor's machine instructions. Each line usually maps to exactly one CPU instruction, so you control precisely what the hardware does. Every architecture (x86-64, ARM, RISC-V) has its own assembly language.

```nasm
mov rax, 5      ; put the number 5 into register rax
add rax, 3      ; rax is now 8
```

Assembly lets you:
- See exactly what the CPU executes, one instruction at a time
- Work directly with registers, flags, and memory addresses
- Understand how high-level code (C, Rust, etc.) is translated to machine code
- Call the operating system directly through system calls
- Write bootloaders, kernels, and firmware
- Squeeze out performance with SIMD and hand-tuned routines
- Read compiler output and disassembled binaries
- Reverse engineer programs and analyze malware
- Debug at the level of individual instructions and registers
- Mix assembly with C through the calling convention

**Prerequisites:** basic programming experience helps, and knowing a little C is ideal. Comfort with binary and hexadecimal is covered in Chapter 4.

## Table of Contents

**Getting Started**  

1. **[Installing an Assembler (NASM, GAS, MASM)](./[1]-Installation-and-Setup.md)**  
    1.1 What Is an Assembler?  
    1.2 Installing NASM (Linux / macOS)  
    1.3 Installing GAS and the GNU Toolchain  
    1.4 Installing MASM (Windows)  
    1.5 Verifying Your Installation  
2. **[Assembling, Linking & Running](./[2]-Assembling-and-Linking.md)**  
    2.1 From Source Code to Executable  
    2.2 Assembling with `nasm`  
    2.3 Linking with `ld` and `gcc`  
    2.4 Your First Program: Exit with a Status Code  
    2.5 Introduction to Makefiles  
3. **[Your Development Environment (editors, debuggers, disassemblers)](./[3]-Development-Environment.md)**  
    3.1 Choosing an Editor  
    3.2 Debugging with GDB (and GEF/pwndbg)  
    3.3 Disassembling with `objdump`  
    3.4 A Recommended Project Layout  

**Foundations**  

4. **[Number Systems & Data Representation (binary, hex, two's complement)](./[4]-Number-Systems.md)**  
    4.1 Binary and Hexadecimal  
    4.2 Bits, Bytes, Words, and Endianness  
    4.3 Signed Numbers and Two's Complement  
    4.4 ASCII and Character Encoding  
5. **[How a CPU Works (fetch, decode, execute)](./[5]-How-a-CPU-Works.md)**  
    5.1 The Fetch-Decode-Execute Cycle  
    5.2 Registers, ALU, and Memory  
    5.3 Instruction Sets and Architectures  
    5.4 Instruction Encoding and Machine Code  
6. **[Registers & the x86-64 Architecture](./[6]-Registers.md)**  
    6.1 General-Purpose Registers  
    6.2 Register Sizes (`rax`, `eax`, `ax`, `al`)  
    6.3 The Instruction Pointer and Flags Register  
    6.4 Segment and Special Registers  
7. **[Assembly Syntax & Program Structure (sections, labels, directives)](./[7]-Syntax-and-Structure.md)**  
    7.1 Instructions, Operands, and Comments  
    7.2 Sections: `.text`, `.data`, `.bss`  
    7.3 Labels and Directives  
    7.4 Intel vs AT&T Syntax  
8. **[Data & Memory Addressing (`mov`, `lea`, addressing modes)](./[8]-Data-and-Addressing.md)**  
    8.1 Defining Data (`db`, `dw`, `dd`, `dq`)  
    8.2 The `mov` Instruction  
    8.3 Addressing Modes  
    8.4 `lea` and Effective Addresses  
    8.5 Reading and Writing Memory  

**Core Instructions**  

9. **[Arithmetic Instructions (`add`, `sub`, `mul`, `div`, `inc`)](./[9]-Arithmetic.md)**  
    9.1 Addition and Subtraction  
    9.2 Multiplication and Division  
    9.3 Increment, Decrement, and Negation  
    9.4 Overflow and Carry  
10. **[Logic & Bitwise Instructions (`and`, `or`, `xor`, `shl`, `shr`)](./[10]-Logic-and-Bitwise.md)**  
    10.1 AND, OR, XOR, NOT  
    10.2 Shifts and Rotates  
    10.3 Masking and Setting Bits  
    10.4 Common Bit Tricks  
11. **[The Flags Register & Comparisons (`cmp`, `test`)](./[11]-Flags-and-Comparisons.md)**  
    11.1 Zero, Carry, Sign, and Overflow Flags  
    11.2 `cmp` and `test`  
    11.3 How Instructions Affect Flags  
12. **[Control Flow: Jumps & Conditionals (`jmp`, `je`, `jne`, `jg`)](./[12]-Control-Flow.md)**  
    12.1 Unconditional Jumps  
    12.2 Conditional Jumps  
    12.3 Signed vs Unsigned Comparisons  
    12.4 Building if/else from Jumps  
13. **[Loops (`loop`, counters, and jump-based loops)](./[13]-Loops.md)**  
    13.1 Counting Loops  
    13.2 While and Do-While Patterns  
    13.3 The `loop` Instruction  
    13.4 Nested Loops  
14. **[Arrays & Strings in Assembly (`rep`, `movs`, `scas`)](./[14]-Arrays-and-Strings.md)**  
    14.1 Indexing Arrays  
    14.2 Null-Terminated Strings  
    14.3 String Instructions and the `rep` Prefix  
    14.4 Computing String Length and Copying  

**The Stack & Functions**  

15. **[The Stack (`push`, `pop`, stack frames)](./[15]-The-Stack.md)**  
    15.1 What Is the Stack?  
    15.2 `push` and `pop`  
    15.3 `rsp` and `rbp`  
    15.4 Stack Frames  
16. **[Procedures: `call` and `ret`](./[16]-Procedures.md)**  
    16.1 Defining and Calling Procedures  
    16.2 Return Addresses  
    16.3 Preserving Registers  
    16.4 Local Variables on the Stack  
17. **[Calling Conventions (System V AMD64, Microsoft x64)](./[17]-Calling-Conventions.md)**  
    17.1 Why Conventions Exist  
    17.2 Passing Arguments in Registers  
    17.3 Return Values  
    17.4 Caller-Saved vs Callee-Saved Registers  
    17.5 Stack Alignment  
18. **[Recursion in Assembly](./[18]-Recursion.md)**  
    18.1 Recursive Calls and the Stack  
    18.2 Factorial and Fibonacci  
    18.3 Avoiding Stack Overflow  

**Talking to the System**  

19. **[System Calls on Linux (`syscall`)](./[19]-System-Calls.md)**  
    19.1 What Is a System Call?  
    19.2 The `syscall` Instruction and Syscall Numbers  
    19.3 Writing to stdout and Reading from stdin  
    19.4 Exiting a Program  
20. **[Input & Output (printing numbers and strings)](./[20]-Input-and-Output.md)**  
    20.1 Printing Strings  
    20.2 Converting Integers to ASCII  
    20.3 Reading User Input  
    20.4 Parsing Numbers  
21. **[Files & Command-Line Arguments](./[21]-Files-and-Arguments.md)**  
    21.1 `open`, `read`, `write`, `close`  
    21.2 Reading `argc` and `argv`  
    21.3 Environment Variables  

**Working with Higher-Level Code**  

22. **[Macros & the Preprocessor](./[22]-Macros.md)**  
    22.1 Single-Line and Multi-Line Macros  
    22.2 Conditional Assembly  
    22.3 Include Files  
23. **[Linking with C (calling C from Assembly and vice versa)](./[23]-Linking-with-C.md)**  
    23.1 Calling `printf` and `scanf`  
    23.2 Writing Assembly Functions Callable from C  
    23.3 Inline Assembly  
    23.4 Object Files and Symbols  
24. **[Reading Compiler Output (C to Assembly)](./[24]-Reading-Compiler-Output.md)**  
    24.1 Generating Assembly with `gcc -S`  
    24.2 Understanding Optimization Levels  
    24.3 Using Compiler Explorer  
25. **[Dynamic Memory & the Heap](./[25]-Dynamic-Memory.md)**  
    25.1 `brk` and `mmap`  
    25.2 Calling `malloc` and `free`  
    25.3 Memory Layout of a Process  

**Floating Point & SIMD**  

26. **[Floating-Point Math (x87 & SSE)](./[26]-Floating-Point.md)**  
    26.1 IEEE 754 Representation  
    26.2 SSE Scalar Instructions  
    26.3 Comparisons and Conversions  
27. **[SIMD Programming (SSE, AVX)](./[27]-SIMD.md)**  
    27.1 What Is SIMD?  
    27.2 XMM and YMM Registers  
    27.3 Packed Arithmetic  
    27.4 Vectorizing a Loop by Hand  

**Beyond x86-64**  

28. **[Introduction to ARM64 Assembly](./[28]-ARM64.md)**  
    28.1 ARM vs x86 Philosophy (RISC vs CISC)  
    28.2 Registers and Instructions  
    28.3 Load/Store Architecture  
    28.4 Calling Convention on ARM64  
29. **[Introduction to RISC-V Assembly](./[29]-RISC-V.md)**  
    29.1 The RISC-V ISA  
    29.2 Registers and Instruction Formats  
    29.3 Running Code with QEMU  
30. **[32-bit x86 & Legacy Modes](./[30]-32-bit-and-Legacy.md)**  
    30.1 Differences from 64-bit  
    30.2 `int 0x80` System Calls  
    30.3 Real Mode and Bootloaders  

**Systems & Security**  

31. **[Writing a Bootloader](./[31]-Writing-a-Bootloader.md)**  
32. **[Interrupts & Exceptions](./[32]-Interrupts-and-Exceptions.md)**  
33. **[Introduction to Reverse Engineering](./[33]-Reverse-Engineering.md)**  
34. **[Buffer Overflows & Stack Exploitation Basics](./[34]-Buffer-Overflows.md)**  
35. **[Shellcode Fundamentals](./[35]-Shellcode.md)**  

**Debugging & Optimization**  

36. **[Debugging with GDB at the Instruction Level](./[36]-Debugging-with-GDB.md)**  
    36.1 Breakpoints and Stepping (`stepi`, `nexti`)  
    36.2 Inspecting Registers and Memory  
    36.3 Disassembling Functions  
37. **[Performance & Optimization (pipelines, caches, branch prediction)](./[37]-Performance-and-Optimization.md)**  
    37.1 CPU Pipelines  
    37.2 Cache and Memory Effects  
    37.3 Branch Prediction  
    37.4 Measuring with `perf`  

**Projects & Best Practices**  

38. **[Project: A Number-Guessing Game](./[38]-Project-Guessing-Game.md)**  
39. **[Project: A Simple Calculator](./[39]-Project-Calculator.md)**  
40. **[Project: A Tiny `cat` Clone](./[40]-Project-Cat-Clone.md)**  
41. **[Style, Commenting & Maintainable Assembly](./[41]-Best-Practices-and-Style.md)**