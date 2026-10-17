[⬅ Back to README](../../../README.md)

<div align="center">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/fortran/fortran-original.svg" width="50"/><br>
  <h1 style="margin-top: 0;">Fortran</h1>
</div>

Welcome! This is a self-paced course for learning Fortran (FORmula TRANslation), the original high-level programming language and still a leading choice for numerical and scientific computing, from weather forecasting and climate models to physics simulations and engineering software.

Download: [https://gcc.gnu.org/wiki/GFortran](https://gcc.gnu.org/wiki/GFortran)

> This course teaches **modern Fortran** (2003, 2008, and 2018) using **gfortran**. You will not need the old punch-card style, though a chapter covers how to read legacy code.

---

## What is Fortran?

Fortran dates to 1957 and has been continuously updated since. It was built for mathematics, so arrays, complex numbers, and floating-point math are built into the language, and compilers optimize them extremely well. Much of the world's high-performance computing (HPC) software is written in it.

```fortran
program hello
    implicit none
    print *, "Hello, World!"
end program hello
```

Fortran lets you:
- Write fast numerical code with whole-array operations
- Work naturally with real, integer, and complex numbers at chosen precision
- Use multidimensional arrays, slicing, and array intrinsics
- Organize code with modules, derived types, and interfaces
- Read and write formatted data and files
- Parallelize across cores and clusters (OpenMP, MPI, coarrays)
- Use mature libraries such as BLAS, LAPACK, and FFTW
- Call C and be called from Python through language interoperability
- Run simulations in physics, chemistry, climate, and engineering
- Maintain and modernize decades-old scientific codebases

**Prerequisites:** basic algebra helps. Programming experience is not required, though it makes the early chapters faster.

## Table of Contents

**Getting Started**  

1. **[Installing a Fortran Compiler (gfortran, ifx, flang)](./[1]-Installation-and-Setup.md)**  
    1.1 What Is a Compiler?  
    1.2 Installing gfortran (Linux / macOS / Windows)  
    1.3 Installing Intel `ifx` and LLVM `flang`  
    1.4 Installing the Fortran Package Manager (`fpm`)  
    1.5 Verifying Your Installation  
2. **[Compiling & Running: Compilers, Modules & `fpm`](./[2]-Compiling-and-Running.md)**  
    2.1 From Source Code to Executable  
    2.2 Compiling a Single File  
    2.3 Compiling Multiple Files and `.mod` Files  
    2.4 Using `fpm` Projects  
    2.5 Common Compiler Flags  
3. **[Your Development Environment (editors, debuggers, warnings)](./[3]-Development-Environment.md)**  
    3.1 Choosing an Editor or IDE  
    3.2 Setting Up a Debugger  
    3.3 Compiler Warnings: `-Wall -Wextra -fcheck=all`  
    3.4 A Recommended Project Layout  

**Core Syntax**  

4. **[Program Structure & `implicit none`](./[4]-Program-Structure.md)**  
    4.1 The `program` Unit  
    4.2 Free-Form Source and Comments  
    4.3 Why `implicit none` Matters  
    4.4 Statements, Continuation Lines, and Case Insensitivity  
5. **[Variables & Basic Data Types](./[5]-Variables-and-Data-Types.md)**  
    5.1 What Is a Variable?  
    5.2 `integer`, `real`, `complex`, `logical`, `character`  
    5.3 Declaring and Initializing Variables  
    5.4 Constants with `parameter`  
    5.5 Naming Conventions  
6. **[Numeric Precision & Kinds (`kind`, `iso_fortran_env`)](./[6]-Kinds-and-Precision.md)**  
    6.1 Single vs Double Precision  
    6.2 The `kind` System  
    6.3 `selected_real_kind` and `real64`  
    6.4 Floating-Point Pitfalls  
    6.5 Type Conversion  
7. **[Operators & Expressions](./[7]-Operators-and-Expressions.md)**  
    7.1 Arithmetic Operators and `**`  
    7.2 Relational Operators  
    7.3 Logical Operators  
    7.4 Operator Precedence  
8. **[Input & Output (`print`, `read`, `write`, format specifiers)](./[8]-Input-and-Output.md)**  
    8.1 List-Directed I/O  
    8.2 Formatted Output (`I`, `F`, `E`, `A`)  
    8.3 Reading from the Keyboard  
    8.4 Format Strings and Repeat Counts  
9. **[Conditionals: `if`, `else if`, `select case`](./[9]-Conditionals.md)**  
    9.1 The `if` Construct  
    9.2 `else if` and `else`  
    9.3 `select case`  
    9.4 Logical `where` Preview  
10. **[Loops: `do`, `do while`, `exit`, `cycle`](./[10]-Loops.md)**  
    10.1 Counted `do` Loops  
    10.2 `do while`  
    10.3 `exit` and `cycle`  
    10.4 Named and Nested Loops  
    10.5 `do concurrent`  

**Arrays & Strings**  

11. **[Arrays Fundamentals](./[11]-Arrays-Fundamentals.md)**  
    11.1 Declaring One- and Multi-Dimensional Arrays  
    11.2 Array Constructors and Initialization  
    11.3 Indexing, Bounds, and Column-Major Order  
    11.4 Array Slicing  
12. **[Whole-Array Operations & Intrinsics (`sum`, `matmul`, `where`)](./[12]-Array-Operations.md)**  
    12.1 Element-wise Arithmetic  
    12.2 `sum`, `product`, `maxval`, `minval`  
    12.3 `matmul`, `dot_product`, `transpose`  
    12.4 `where`, `forall`, and Masking  
13. **[Allocatable Arrays & Dynamic Memory (`allocate`, `deallocate`)](./[13]-Allocatable-Arrays.md)**  
    13.1 Why Dynamic Arrays?  
    13.2 `allocate` and `deallocate`  
    13.3 Automatic Reallocation  
    13.4 Pointers vs Allocatables  
14. **[Strings & Characters](./[14]-Strings.md)**  
    14.1 Fixed-Length Strings and `trim`  
    14.2 Substrings and Concatenation  
    14.3 `len`, `index`, `scan`, `verify`  
    14.4 Deferred-Length `character(len=:)`  

**Procedures & Modules**  

15. **[Functions & Subroutines](./[15]-Functions-and-Subroutines.md)**  
    15.1 Declaring and Calling Subroutines  
    15.2 Functions and Return Values  
    15.3 `intent(in)`, `intent(out)`, `intent(inout)`  
    15.4 Optional and Keyword Arguments  
16. **[Modules & `use` Statements](./[16]-Modules.md)**  
    16.1 Why Modules?  
    16.2 `contains`, `public`, and `private`  
    16.3 `use`, `only`, and Renaming  
    16.4 Explicit Interfaces  
17. **[Recursion & Pure/Elemental Procedures](./[17]-Recursion-and-Pure-Procedures.md)**  
    17.1 `recursive` Procedures  
    17.2 `pure` and `elemental`  
    17.3 Passing Arrays and Procedures as Arguments  
18. **[Scope, `save`, and Variable Lifetime](./[18]-Scope-and-Lifetime.md)**  

**Data Structures**  

19. **[Derived Types](./[19]-Derived-Types.md)**  
    19.1 Defining and Using Types  
    19.2 Arrays of Derived Types  
    19.3 Default Initialization  
20. **[Type-Bound Procedures & Object-Oriented Fortran](./[20]-Object-Oriented-Fortran.md)**  
    20.1 `type`, `class`, and `extends`  
    20.2 Type-Bound Procedures  
    20.3 Inheritance and Polymorphism  
    20.4 Abstract Types  
21. **[Pointers & Linked Data Structures](./[21]-Pointers-and-Linked-Lists.md)**  
22. **[Generic Programming (interfaces, operator overloading, `generic`)](./[22]-Generic-Programming.md)**  
23. **[Parameterized Derived Types & Templates](./[23]-Parameterized-Types.md)**  

**Files & Standard Library**  

24. **[File I/O (`open`, `read`, `write`, `close`)](./[24]-File-IO.md)**  
    24.1 Opening and Closing Files  
    24.2 Formatted vs Unformatted I/O  
    24.3 Stream and Direct Access  
    24.4 Reading CSV and Tabular Data  
25. **[Namelists & Configuration Files](./[25]-Namelists.md)**  
26. **[Error Handling (`iostat`, `iomsg`, `error stop`)](./[26]-Error-Handling.md)**  
27. **[Command-Line Arguments & Environment (`get_command_argument`)](./[27]-Command-Line-Arguments.md)**  
28. **[Intrinsic Functions & Math (`sqrt`, `sin`, `exp`, `random_number`)](./[28]-Intrinsic-Functions.md)**  
29. **[Date, Time & Timing (`date_and_time`, `cpu_time`)](./[29]-Date-and-Time.md)**  

**Numerical Computing**  

30. **[Numerical Methods (root finding, integration, ODEs)](./[30]-Numerical-Methods.md)**  
    30.1 Newton's Method and Bisection  
    30.2 Numerical Integration  
    30.3 Solving Ordinary Differential Equations  
31. **[Linear Algebra with BLAS & LAPACK](./[31]-Linear-Algebra.md)**  
32. **[Random Numbers & Monte Carlo Simulation](./[32]-Monte-Carlo.md)**  
33. **[Fast Fourier Transforms & Signal Processing](./[33]-FFT.md)**  
34. **[Plotting & Visualizing Results (gnuplot, Python, matplotlib)](./[34]-Plotting.md)**  

**Parallel & High-Performance Computing**  

35. **[Performance Basics (memory layout, vectorization, profiling)](./[35]-Performance-Basics.md)**  
36. **[OpenMP: Shared-Memory Parallelism](./[36]-OpenMP.md)**  
37. **[Coarrays & Fortran's Native Parallelism](./[37]-Coarrays.md)**  
38. **[MPI: Distributed-Memory Parallelism](./[38]-MPI.md)**  
39. **[GPU Computing with OpenACC & `do concurrent`](./[39]-GPU-Computing.md)**  

**Interoperability**  

40. **[Calling C from Fortran (`iso_c_binding`)](./[40]-Calling-C.md)**  
41. **[Calling Fortran from C and Python (`f2py`)](./[41]-Calling-Fortran.md)**  

**Legacy Code & Tooling**  

42. **[Reading Legacy Fortran (fixed-form, `common`, `goto`, `equivalence`)](./[42]-Legacy-Fortran.md)**  
    42.1 Fixed-Form Source and Column Rules  
    42.2 `common` Blocks and `implicit` Typing  
    42.3 `goto`, Arithmetic `if`, and Computed `goto`  
    42.4 Modernizing Legacy Code  
43. **[Build Systems: Make, CMake & `fpm`](./[43]-Build-Systems.md)**  
44. **[Debugging with GDB & Runtime Checks](./[44]-Debugging.md)**  
45. **[Unit Testing (test-drive, pFUnit)](./[45]-Unit-Testing.md)**  
46. **[Documentation with FORD](./[46]-Documentation.md)**  

**Projects & Best Practices**  

47. **[Project: Matrix Library](./[47]-Project-Matrix-Library.md)**  
48. **[Project: Heat Equation Simulator](./[48]-Project-Heat-Equation.md)**  
49. **[Project: N-Body Simulation](./[49]-Project-N-Body.md)**  
50. **[Fortran Style Guides & Idiomatic Modern Fortran](./[50]-Best-Practices-and-Style.md)**  
51. **[Fortran Standards & Compiler Portability (77 to 2023)](./[51]-Standards-and-Portability.md)**