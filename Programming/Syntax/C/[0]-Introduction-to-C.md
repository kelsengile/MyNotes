[⬅ Back to README](../../../README.md)

# C

Welcome! This is a self-paced course for learning C, a low-level, procedural programming language that underpins operating systems, embedded devices, compilers, and performance-critical software.

---

## What is C?

C lets you:
- Write fast, portable code that runs close to the hardware
- Manage memory manually with full control over allocation and layout
- Build operating systems, drivers, embedded firmware, and compilers
- Interface directly with the OS through system calls
- Implement your own data structures and algorithms from scratch
- Work with pointers, arrays, and raw memory
- Organize code into multi-file projects with headers and build systems
- Interoperate with nearly every other language via the C ABI
- Write concurrent and networked programs
- Debug and profile at the level of individual instructions

## Table of Contents

**Getting Started**  
    1. **[Installing a C Toolchain (GCC, Clang, MSVC)](./[1]-Installation-and-Setup.md)**  
       1.1 What Is a Toolchain?  
       1.2 Installing GCC (Linux / macOS)  
       1.3 Installing Clang  
       1.4 Installing MSVC (Windows)  
       1.5 Verifying Your Installation  
    2. **[Compiling & Running: Compilers, Linkers & Makefiles](./[2]-Compiling-and-Running.md)**  
       2.1 From Source Code to Executable  
       2.2 Compiling a Single File  
       2.3 The Linker and Multiple Files  
       2.4 Introduction to Makefiles  
       2.5 Common Compiler Flags  
    3. **[Your Development Environment (editors, debuggers, `-Wall -Wextra`)](./[3]-Development-Environment.md)**  
       3.1 Choosing an Editor or IDE  
       3.2 Setting Up a Debugger  
       3.3 Compiler Warnings: -Wall and -Wextra  
       3.4 A Recommended Project Layout  

**Core Syntax**  
    4. **[Variables & Basic Data Types](./[4]-Variables-and-Data-Types.md)**  
       4.1 What Is a Variable?  
       4.2 Declaring and Initializing Variables  
       4.3 Basic Data Types Overview  
       4.4 Constants  
       4.5 Naming Conventions  
    5. **[Integers, Floats, Chars & Type Sizes](./[5]-Numbers-and-Characters.md)**  
       5.1 Integer Types and Signedness  
       5.2 Floating-Point Types  
       5.3 The char Type  
       5.4 Type Sizes and sizeof  
       5.5 Type Conversion and Casting  
    6. **[Operators & Expressions (arithmetic, comparison, logical, bitwise)](./[6]-Operators-and-Expressions.md)**  
       6.1 Arithmetic Operators  
       6.2 Comparison Operators  
       6.3 Logical Operators  
       6.4 Bitwise Operators  
       6.5 Assignment and Compound Assignment  
       6.6 Operator Precedence  
    7. **[Conditionals: if, else, switch](./[7]-Conditionals.md)**  
       7.1 The if Statement  
       7.2 else and else if  
       7.3 The switch Statement  
       7.4 The Ternary Operator  
    8. **[Loops: for, while, do-while, break, continue](./[8]-Loops.md)**  
       8.1 The for Loop  
       8.2 The while Loop  
       8.3 The do-while Loop  
       8.4 break and continue  
       8.5 Nested Loops  
    9. **[Functions & Scope (declarations, prototypes, recursion)](./[9]-Functions-and-Scope.md)**  
       9.1 Declaring and Defining Functions  
       9.2 Function Prototypes  
       9.3 Parameters and Return Values  
       9.4 Scope and Lifetime of Variables  
       9.5 Recursion  
    10. **[The Preprocessor (`#define`, `#include`, macros, conditional compilation)](./[10]-The-Preprocessor.md)**  
        10.1 What Is the Preprocessor?  
        10.2 #include  
        10.3 #define and Macros  
        10.4 Conditional Compilation  
        10.5 Common Pitfalls with Macros  

**Pointers & Memory**  
    11. **[Pointers Fundamentals (address-of, dereference, pointer arithmetic)](./[11]-Pointers-Fundamentals.md)**  
        11.1 What Is a Pointer?  
        11.2 The Address-Of Operator  
        11.3 Dereferencing a Pointer  
        11.4 Pointer Arithmetic  
        11.5 Null Pointers and Common Mistakes  
    12. **[Arrays & Strings (char arrays, null termination, `string.h`)](./[12]-Arrays-and-Strings.md)**  
        12.1 Declaring and Using Arrays  
        12.2 Arrays and Pointers  
        12.3 Strings as Char Arrays  
        12.4 Null Termination  
        12.5 The string.h Library  
    13. **[Dynamic Memory (`malloc`, `calloc`, `realloc`, `free`)](./[13]-Dynamic-Memory.md)**  
        13.1 Why Dynamic Memory?  
        13.2 malloc and free  
        13.3 calloc  
        13.4 realloc  
        13.5 Avoiding Leaks and Dangling Pointers  
    14. **[Memory Layout (stack, heap, data & text segments)](./[14]-Memory-Layout.md)**  
        14.1 The Stack  
        14.2 The Heap  
        14.3 Data and BSS Segments  
        14.4 The Text Segment  
        14.5 Putting It All Together  
    15. **[Common Memory Bugs (leaks, dangling pointers, buffer overflows)](./[15]-Common-Memory-Bugs.md)**  
        15.1 Memory Leaks  
        15.2 Dangling Pointers  
        15.3 Buffer Overflows  
        15.4 Use-After-Free  
        15.5 Double Free  
    16. **[Function Pointers & Callbacks](./[16]-Function-Pointers.md)**  
        16.1 What Is a Function Pointer?  
        16.2 Declaring and Using Function Pointers  
        16.3 Callbacks  
        16.4 Arrays of Function Pointers  

**Data Structures in C**  
    17. **[Structs & Typedefs](./[17]-Structs-and-Typedefs.md)**  
    18. **[Unions & Bit Fields](./[18]-Unions-and-Bit-Fields.md)**  
    19. **[Enums](./[19]-Enums.md)**  
    20. **[Building Linked Lists, Stacks & Queues](./[20]-Linked-Lists-Stacks-Queues.md)**  
    21. **[Building Trees & Hash Tables](./[21]-Trees-and-Hash-Tables.md)**  

**Multi-File Projects**  
    22. **[Header Files & Include Guards](./[22]-Header-Files-and-Include-Guards.md)**  
    23. **[Separate Compilation & Linking](./[23]-Separate-Compilation-and-Linking.md)**  
    24. **[Build Systems: Make & CMake](./[24]-Make-and-CMake.md)**  
    25. **[Static & Dynamic Libraries](./[25]-Static-and-Dynamic-Libraries.md)**  

**Standard Library**  
    26. **[File I/O (`stdio.h`, `fopen`, `fread`, `fwrite`)](./[26]-File-IO.md)**  
    27. **[String Handling (`string.h` deep dive)](./[27]-String-Handling.md)**  
    28. **[Math Library (`math.h`)](./[28]-Math-Library.md)**  
    29. **[Time & Date (`time.h`)](./[29]-Time-and-Date.md)**  
    30. **[Error Handling (`errno`, `perror`, return codes)](./[30]-Error-Handling.md)**  
    31. **[Command-Line Arguments (`argc`, `argv`, `getopt`)](./[31]-Command-Line-Arguments.md)**  

**Systems Programming**  
    32. **[Process Management (`fork`, `exec`, `wait`)](./[32]-Process-Management.md)**  
    33. **[Signals & Signal Handling](./[33]-Signals.md)**  
    34. **[File Descriptors & Low-Level I/O (`read`, `write`, `open`)](./[34]-File-Descriptors.md)**  
    35. **[Inter-Process Communication (pipes, shared memory, message queues)](./[35]-IPC.md)**  
    36. **[Memory-Mapped Files (`mmap`)](./[36]-Memory-Mapped-Files.md)**  

**Concurrency**  
    37. **[POSIX Threads (`pthread`)](./[37]-POSIX-Threads.md)**  
    38. **[Mutexes, Condition Variables & Semaphores](./[38]-Mutexes-and-Semaphores.md)**  
    39. **[Atomic Operations & `stdatomic.h`](./[39]-Atomic-Operations.md)**  

**Networking**  
    40. **[Sockets Programming (TCP/UDP)](./[40]-Sockets-Programming.md)**  
    41. **[Building a Simple Client-Server App](./[41]-Client-Server-App.md)**  

**Advanced Language Features**  
    42. **[Const-Correctness & `volatile`](./[42]-Const-and-Volatile.md)**  
    43. **[Variadic Functions (`stdarg.h`)](./[43]-Variadic-Functions.md)**  
    44. **[Generic Programming with Macros & `_Generic`](./[44]-Generic-Programming.md)**  
    45. **[Undefined Behavior & Sequence Points](./[45]-Undefined-Behavior.md)**  
    46. **[Inline Assembly Basics](./[46]-Inline-Assembly.md)**  

**Debugging & Testing**  
    47. **[Debugging with GDB/LLDB](./[47]-Debugging-with-GDB.md)**  
    48. **[Memory Debugging: Valgrind & AddressSanitizer](./[48]-Valgrind-and-Sanitizers.md)**  
    49. **[Unit Testing in C (Unity, Check, CMocka)](./[49]-Unit-Testing.md)**  
    50. **[Profiling & Optimization (`gprof`, `perf`)](./[50]-Profiling-and-Optimization.md)**  

**Best Practices**  
    51. **[C Style Guides & Idiomatic C](./[51]-Best-Practices-and-Style.md)**  
    52. **[Portability Across Compilers & Platforms](./[52]-Portability.md)**  
    53. **[Secure C Coding Practices](./[53]-Secure-Coding-Practices.md)**