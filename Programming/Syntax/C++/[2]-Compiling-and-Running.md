[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[3]-Development-Environment.md)

*Getting Started*

# Lesson 2 - Compiling And Running

## 2.1 The Compilation Pipeline

Turning a `.cpp` file into a runnable program happens in four stages:

1. **Preprocessing** — handles directives like `#include` and `#define`, expanding them into plain C++ source.
2. **Compilation** — translates the preprocessed source into assembly, then into an **object file** (`.o` or `.obj`) containing machine code.
3. **Assembly** — the compiler's assembler turns the assembly into binary object code (often treated as part of step 2).
4. **Linking** — combines one or more object files with any required libraries into a final **executable**.

You rarely run these stages manually — a single compiler command does all four for you.

---

## 2.2 Compiling A Single File

Given a file `hello.cpp`:

```cpp
#include <iostream>

int main() {
    std::cout << "Hello, C++!\n";
    return 0;
}
```

Compile and run it with GCC or Clang:

```bash
g++ hello.cpp -o hello
./hello
```

With MSVC, from a Developer Command Prompt:

```bash
cl hello.cpp
hello.exe
```

The `-o hello` flag tells the compiler what to name the output executable. Without it, GCC/Clang default to `a.out` (or `a.exe` on Windows).

---

## 2.3 Compiling Multiple Files & Linking

Real programs are split across multiple `.cpp` files. Each is compiled into its own object file, then linked together:

```bash
g++ -c main.cpp -o main.o
g++ -c helpers.cpp -o helpers.o
g++ main.o helpers.o -o program
./program
```

The `-c` flag tells the compiler to stop after producing an object file, without linking. This lets you recompile only the files that changed, which is much faster for large projects.

---

## 2.4 Intro To Build Systems

Typing compiler commands by hand doesn't scale to real projects with dozens of files. **Build systems** automate this:

- **Make** — reads a `Makefile` describing how files depend on each other and only rebuilds what changed.
- **CMake** — generates project files for Make, Ninja, Visual Studio, and others from a single cross-platform description (`CMakeLists.txt`).
- **Ninja** — a fast, low-level build tool often used as CMake's backend.

A minimal `CMakeLists.txt` looks like:

```cmake
cmake_minimum_required(VERSION 3.10)
project(HelloWorld)
add_executable(hello hello.cpp)
```

Build systems are covered in depth later in this course, in **Build Systems: CMake & Package Managers**.

[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[3]-Development-Environment.md)
