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
    2. **[Compiling & Running: Compilers, Linkers & Build Systems](./[2]-Compiling-and-Running.md)**  
    3. **[Your Development Environment (editors, debuggers, compiler flags)](./[3]-Development-Environment.md)**  
    4. **[C++ Standards & Compiler Support (C++11–C++23)](./[4]-CPP-Standards.md)**  

**Core Syntax**  
    5. **[Variables & Basic Data Types](./[5]-Variables-and-Data-Types.md)**  
    6. **[Numbers, Strings & Booleans](./[6]-Numbers-Strings-and-Booleans.md)**  
    7. **[Operators & Expressions (arithmetic, comparison, logical, bitwise)](./[7]-Operators-and-Expressions.md)**  
    8. **[Conditionals: if, else, switch](./[8]-Conditionals.md)**  
    9. **[Loops: for, range-based for, while, do-while](./[9]-Loops.md)**  
    10. **[Functions & Scope (overloading, default args, inline)](./[10]-Functions-and-Scope.md)**  
    11. **[References vs Pointers](./[11]-References-vs-Pointers.md)**  
    12. **[Error Handling: exceptions, try/catch, noexcept](./[12]-Error-Handling.md)**  

**Memory Management**  
    13. **[Stack vs Heap & Memory Layout](./[13]-Stack-vs-Heap.md)**  
    14. **[Dynamic Memory (`new`, `delete`)](./[14]-Dynamic-Memory.md)**  
    15. **[RAII (Resource Acquisition Is Initialization)](./[15]-RAII.md)**  
    16. **[Smart Pointers (`unique_ptr`, `shared_ptr`, `weak_ptr`)](./[16]-Smart-Pointers.md)**  
    17. **[Move Semantics & Rvalue References](./[17]-Move-Semantics.md)**  

**Data Structures**  
    18. **[Arrays & C-Style Strings](./[18]-Arrays-and-C-Strings.md)**  
    19. **[`std::string` & String Manipulation](./[19]-std-string.md)**  
    20. **[STL Containers: vector, array, deque](./[20]-STL-Sequence-Containers.md)**  
    21. **[STL Containers: map, set, unordered_map, unordered_set](./[21]-STL-Associative-Containers.md)**  
    22. **[STL Iterators & Algorithms](./[22]-STL-Iterators-and-Algorithms.md)**  

**Object-Oriented Programming**  
    23. **[Classes & Objects](./[23]-OOP-Classes-and-Objects.md)**  
    24. **[Constructors, Destructors & the Rule of Three/Five/Zero](./[24]-Rule-of-Three-Five-Zero.md)**  
    25. **[Inheritance & Polymorphism (virtual functions, vtables)](./[25]-Inheritance-and-Polymorphism.md)**  
    26. **[Encapsulation & Access Control](./[26]-Encapsulation-and-Access-Control.md)**  
    27. **[Operator Overloading](./[27]-Operator-Overloading.md)**  
    28. **[Abstract Classes & Interfaces](./[28]-Abstract-Classes-and-Interfaces.md)**  
    29. **[Multiple Inheritance & Virtual Inheritance](./[29]-Multiple-and-Virtual-Inheritance.md)**  

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