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
    2. **[Compiling & Running: Compilers, Linkers & Makefiles](./[2]-Compiling-and-Running.md)**  
    3. **[Your Development Environment (editors, debuggers, `-Wall -Wextra`)](./[3]-Development-Environment.md)**  

**Core Syntax**  
    4. **[Variables & Basic Data Types](./[4]-Variables-and-Data-Types.md)**  
    5. **[Integers, Floats, Chars & Type Sizes](./[5]-Numbers-and-Characters.md)**  
    6. **[Operators & Expressions (arithmetic, comparison, logical, bitwise)](./[6]-Operators-and-Expressions.md)**  
    7. **[Conditionals: if, else, switch](./[7]-Conditionals.md)**  
    8. **[Loops: for, while, do-while, break, continue](./[8]-Loops.md)**  
    9. **[Functions & Scope (declarations, prototypes, recursion)](./[9]-Functions-and-Scope.md)**  
    10. **[The Preprocessor (`#define`, `#include`, macros, conditional compilation)](./[10]-The-Preprocessor.md)**  

**Pointers & Memory**  
    11. **[Pointers Fundamentals (address-of, dereference, pointer arithmetic)](./[11]-Pointers-Fundamentals.md)**  
    12. **[Arrays & Strings (char arrays, null termination, `string.h`)](./[12]-Arrays-and-Strings.md)**  
    13. **[Dynamic Memory (`malloc`, `calloc`, `realloc`, `free`)](./[13]-Dynamic-Memory.md)**  
    14. **[Memory Layout (stack, heap, data & text segments)](./[14]-Memory-Layout.md)**  
    15. **[Common Memory Bugs (leaks, dangling pointers, buffer overflows)](./[15]-Common-Memory-Bugs.md)**  
    16. **[Function Pointers & Callbacks](./[16]-Function-Pointers.md)**  

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