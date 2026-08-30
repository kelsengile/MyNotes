[⬅ Back to README](../../../README.md)

# C++


Welcome! This is a self-paced course for learning C++, a high-performance, multi-paradigm language combining low-level control with powerful abstractions, used in games, systems, finance, and embedded software.

---

## What is C++?

C++ lets you:
- Write high-performance code with fine-grained control over memory and hardware
- Build on C's foundation with object-oriented and generic programming
- Manage resources safely with RAII and smart pointers
- Write reusable, type-safe code with templates and the STL
- Take advantage of modern language features (C++11 through C++23)
- Build game engines, real-time systems, and high-frequency trading software
- Write concurrent and parallel programs
- Interface directly with hardware and operating systems
- Package and distribute cross-platform libraries and applications
- Optimize for speed with profiling and low-level tuning

## Table of Contents

**Getting Started**  
    1. **[Installing a C++ Toolchain (GCC, Clang, MSVC)](./[1]-Installation-and-Setup.md)**  
       1.1 Why You Need A Toolchain  
       1.2 Installing GCC (Linux/macOS)  
       1.3 Installing Clang  
       1.4 Installing MSVC (Windows)  
       1.5 Verifying Your Installation  
    2. **[Compiling & Running: Compilers, Linkers & Build Systems](./[2]-Compiling-and-Running.md)**  
       2.1 The Compilation Pipeline  
       2.2 Compiling A Single File  
       2.3 Compiling Multiple Files & Linking  
       2.4 Intro To Build Systems  
    3. **[Your Development Environment (editors, debuggers, compiler flags)](./[3]-Development-Environment.md)**  
       3.1 Choosing An Editor/IDE  
       3.2 Useful Compiler Flags  
       3.3 Debugging Basics (gdb/lldb)  
       3.4 Project Layout Conventions  
    4. **[C++ Standards & Compiler Support (C++11–C++23)](./[4]-CPP-Standards.md)**  
       4.1 What Is A C++ Standard  
       4.2 Overview Of C++11 Through C++23  
       4.3 Specifying The Standard When Compiling  
       4.4 Checking Feature Support  

**Core Syntax**  
    5. **[Variables & Basic Data Types](./[5]-Variables-and-Data-Types.md)**  
       5.1 Declaring Variables  
       5.2 Fundamental Data Types  
       5.3 Type Sizes And sizeof  
       5.4 Constants (const, constexpr)  
       5.5 Type Conversion Basics  
    6. **[Numbers, Strings & Booleans](./[6]-Numbers-Strings-and-Booleans.md)**  
       6.1 Integer Types And Overflow  
       6.2 Floating-Point Numbers  
       6.3 Booleans  
       6.4 Character And String Literals  
       6.5 Escape Sequences  
    7. **[Operators & Expressions (arithmetic, comparison, logical, bitwise)](./[7]-Operators-and-Expressions.md)**  
       7.1 Arithmetic Operators  
       7.2 Comparison Operators  
       7.3 Logical Operators  
       7.4 Bitwise Operators  
       7.5 Operator Precedence & Assignment Operators  
    8. **[Conditionals: if, else, switch](./[8]-Conditionals.md)**  
       8.1 if / else if / else  
       8.2 switch Statements  
       8.3 Ternary Operator  
       8.4 Nested Conditionals & Best Practices  
    9. **[Loops: for, range-based for, while, do-while](./[9]-Loops.md)**  
       9.1 for Loops  
       9.2 Range-Based for Loops  
       9.3 while And do-while Loops  
       9.4 break, continue, And Infinite Loops  
    10. **[Functions & Scope (overloading, default args, inline)](./[10]-Functions-and-Scope.md)**  
        10.1 Declaring And Defining Functions  
        10.2 Parameters, Return Values & Default Arguments  
        10.3 Function Overloading  
        10.4 Scope And Lifetime  
        10.5 inline Functions  
    11. **[References vs Pointers](./[11]-References-vs-Pointers.md)**  
        11.1 What Is A Pointer  
        11.2 What Is A Reference  
        11.3 Pointers Vs References: Key Differences  
        11.4 Pointer Arithmetic Basics  
        11.5 const With Pointers And References  
    12. **[Error Handling: exceptions, try/catch, noexcept](./[12]-Error-Handling.md)**  
        12.1 Why Error Handling Matters  
        12.2 Exceptions: throw, try, catch  
        12.3 Standard Exception Types  
        12.4 noexcept  
        12.5 Best Practices For Exceptions  

**Memory Management**  
    13. **[Stack vs Heap & Memory Layout](./[13]-Stack-vs-Heap.md)**  
        13.1 Program Memory Layout  
        13.2 The Stack  
        13.3 The Heap  
        13.4 Stack Vs Heap Trade-Offs  
    14. **[Dynamic Memory (`new`, `delete`)](./[14]-Dynamic-Memory.md)**  
        14.1 Allocating With new  
        14.2 Freeing With delete  
        14.3 Arrays On The Heap  
        14.4 Common Pitfalls (Leaks, Dangling Pointers, Double Free)  
    15. **[RAII (Resource Acquisition Is Initialization)](./[15]-RAII.md)**  
        15.1 The Problem RAII Solves  
        15.2 How RAII Works  
        15.3 RAII In Practice  
        15.4 RAII Beyond Memory  
    16. **[Smart Pointers (`unique_ptr`, `shared_ptr`, `weak_ptr`)](./[16]-Smart-Pointers.md)**  
        16.1 Why Smart Pointers  
        16.2 unique_ptr  
        16.3 shared_ptr  
        16.4 weak_ptr  
        16.5 Choosing The Right Smart Pointer  
    17. **[Move Semantics & Rvalue References](./[17]-Move-Semantics.md)**  
        17.1 Lvalues And Rvalues  
        17.2 Rvalue References  
        17.3 Move Constructors And Move Assignment  
        17.4 std::move  
        17.5 When Moves Happen Automatically  

**Data Structures**  
    18. **[Arrays & C-Style Strings](./[18]-Arrays-and-C-Strings.md)**  
        18.1 Fixed-Size Arrays  
        18.2 Arrays And Pointer Decay  
        18.3 C-Style Strings  
        18.4 Common Pitfalls  
    19. **[`std::string` & String Manipulation](./[19]-std-string.md)**  
        19.1 Creating And Initializing Strings  
        19.2 Concatenation And Comparison  
        19.3 Accessing And Modifying Characters  
        19.4 Searching And Substrings  
        19.5 Converting Between Strings And Numbers  
    20. **[STL Containers: vector, array, deque](./[20]-STL-Sequence-Containers.md)**  
        20.1 std::vector  
        20.2 std::array  
        20.3 std::deque  
        20.4 Choosing A Sequence Container  
    21. **[STL Containers: map, set, unordered_map, unordered_set](./[21]-STL-Associative-Containers.md)**  
        21.1 std::map  
        21.2 std::set  
        21.3 std::unordered_map And std::unordered_set  
        21.4 Choosing An Associative Container  
    22. **[STL Iterators & Algorithms](./[22]-STL-Iterators-and-Algorithms.md)**  
        22.1 What Is An Iterator  
        22.2 Iterator Categories  
        22.3 Common Algorithms (sort, find, count, Etc.)  
        22.4 Lambdas With Algorithms (Brief Preview)  

**Object-Oriented Programming**  
    23. **[Classes & Objects](./[23]-OOP-Classes-and-Objects.md)**  
        23.1 Defining A Class  
        23.2 Member Variables And Member Functions  
        23.3 Creating Objects  
        23.4 The this Pointer  
    24. **[Constructors, Destructors & the Rule of Three/Five/Zero](./[24]-Rule-of-Three-Five-Zero.md)**  
        24.1 Constructors  
        24.2 Destructors  
        24.3 Copy Constructor And Copy Assignment  
        24.4 The Rule Of Three  
        24.5 The Rule Of Five And Rule Of Zero  
    25. **[Inheritance & Polymorphism (virtual functions, vtables)](./[25]-Inheritance-and-Polymorphism.md)**  
        25.1 Basic Inheritance  
        25.2 Overriding Behavior With virtual  
        25.3 How vtables Work (Conceptually)  
        25.4 Polymorphism In Practice  
    26. **[Encapsulation & Access Control](./[26]-Encapsulation-and-Access-Control.md)**  
        26.1 public, private, protected  
        26.2 Getters And Setters  
        26.3 Friend Functions And Classes  
        26.4 Why Encapsulation Matters  
    27. **[Operator Overloading](./[27]-Operator-Overloading.md)**  
        27.1 Why Overload Operators  
        27.2 Overloading Arithmetic Operators  
        27.3 Overloading Comparison Operators  
        27.4 Overloading << And >>  
        27.5 Rules And Best Practices  
    28. **[Abstract Classes & Interfaces](./[28]-Abstract-Classes-and-Interfaces.md)**  
        28.1 Pure Virtual Functions  
        28.2 Abstract Classes  
        28.3 Interfaces In C++  
        28.4 Virtual Destructors  
    29. **[Multiple Inheritance & Virtual Inheritance](./[29]-Multiple-and-Virtual-Inheritance.md)**  
        29.1 What Is Multiple Inheritance  
        29.2 The Diamond Problem  
        29.3 Virtual Inheritance  
        29.4 Best Practices And Alternatives  

**Generic Programming**  
    30. **[Function Templates](./[30]-Function-Templates.md)**  
    31. **[Class Templates](./[31]-Class-Templates.md)**  
    32. **[Template Specialization & SFINAE](./[32]-Template-Specialization-and-SFINAE.md)**  
    33. **[Variadic Templates](./[33]-Variadic-Templates.md)**  
    34. **[Concepts (C++20)](./[34]-Concepts.md)**  

**Modern C++ Features**  
    35. **[`auto`, `decltype` & Type Deduction](./[35]-Auto-and-Type-Deduction.md)**  
    36. **[Lambda Expressions & Closures](./[36]-Lambda-Expressions.md)**  
    37. **[`constexpr`, `consteval` & Compile-Time Programming](./[37]-Constexpr-and-Compile-Time.md)**  
    38. **[Structured Bindings & `std::optional`/`std::variant`](./[38]-Structured-Bindings-Optional-Variant.md)**  
    39. **[Ranges & Views (C++20)](./[39]-Ranges-and-Views.md)**  
    40. **[Modules (C++20)](./[40]-Modules.md)**  
    41. **[Coroutines (C++20)](./[41]-Coroutines.md)**  

**Namespaces & Project Structure**  
    42. **[Namespaces](./[42]-Namespaces.md)**  
    43. **[Header Files, Include Guards & `#pragma once`](./[43]-Header-Files.md)**  
    44. **[Separate Compilation & Linking](./[44]-Separate-Compilation-and-Linking.md)**  
    45. **[Build Systems: CMake & Package Managers (vcpkg, Conan)](./[45]-CMake-and-Package-Managers.md)**  

**Concurrency**  
    46. **[Threads (`std::thread`)](./[46]-Threads.md)**  
    47. **[Mutexes, Locks & Condition Variables](./[47]-Mutexes-and-Condition-Variables.md)**  
    48. **[Atomics & Memory Ordering](./[48]-Atomics-and-Memory-Ordering.md)**  
    49. **[`std::async`, Futures & Promises](./[49]-Async-Futures-Promises.md)**  

**Standard Library**  
    50. **[Working with Files (fstream, filesystem)](./[50]-Working-with-Files.md)**  
    51. **[Working with Dates & Times (`<chrono>`)](./[51]-Chrono.md)**  
    52. **[Regular Expressions (`<regex>`)](./[52]-Regular-Expressions.md)**  
    53. **[Random Numbers (`<random>`)](./[53]-Random-Numbers.md)**  
    54. **[Data Serialization (JSON with nlohmann/json)](./[54]-Data-Serialization.md)**  

**Networking**  
    55. **[Sockets Programming (Boost.Asio, POSIX sockets)](./[55]-Sockets-Programming.md)**  
    56. **[HTTP Clients & REST APIs in C++](./[56]-HTTP-Clients.md)**  

**Security**  
    57. **[Hashing & Encryption (OpenSSL, libsodium)](./[57]-Hashing-and-Encryption.md)**  
    58. **[Secure Coding Practices](./[58]-Secure-Coding-Practices.md)**  

**Design Patterns & Architecture**  
    59. **[SOLID Principles in C++](./[59]-SOLID-Principles.md)**  
    60. **[Creational Patterns (Singleton, Factory, Builder)](./[60]-Creational-Design-Patterns.md)**  
    61. **[Structural Patterns (Adapter, Decorator, Proxy)](./[61]-Structural-Design-Patterns.md)**  
    62. **[Behavioral Patterns (Observer, Strategy, Command)](./[62]-Behavioral-Design-Patterns.md)**  

**Testing & Code Quality**  
    63. **[Unit Testing (Google Test, Catch2)](./[63]-Unit-Testing.md)**  
    64. **[Mocking (Google Mock)](./[64]-Mocking.md)**  
    65. **[Linters & Formatters (clang-tidy, clang-format)](./[65]-Linters-and-Formatters.md)**  
    66. **[Static Analysis & Sanitizers (ASan, UBSan, TSan)](./[66]-Static-Analysis-and-Sanitizers.md)**  

**Packaging & Deployment**  
    67. **[Static & Dynamic Libraries](./[67]-Static-and-Dynamic-Libraries.md)**  
    68. **[Docker Basics for C++ Apps](./[68]-Docker-Basics.md)**  
    69. **[CI/CD Basics (GitHub Actions)](./[69]-CICD-Basics.md)**  

**Performance & Optimization**  
    70. **[Profiling (perf, Valgrind, VTune)](./[70]-Profiling.md)**  
    71. **[Cache-Friendly Code & Data-Oriented Design](./[71]-Cache-Friendly-Code.md)**  
    72. **[SIMD & Vectorization Basics](./[72]-SIMD-and-Vectorization.md)**  

**Best Practices**  
    73. **[C++ Core Guidelines & Idiomatic C++](./[73]-Best-Practices-and-Style.md)**