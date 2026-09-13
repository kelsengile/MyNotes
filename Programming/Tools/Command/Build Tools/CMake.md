[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# CMake

CMake is a cross-platform **build system generator** — it doesn't compile your code itself, but reads a `CMakeLists.txt` file and generates the native build files (Makefiles, Ninja files, Visual Studio projects, and more) for whatever platform you're on.

Download: [https://cmake.org/download/](https://cmake.org/download/)

---

## What Is CMake?

Writing a Makefile by hand that works identically on Linux, macOS, and Windows is painful. CMake solves this by letting you describe your project once, then generating the right build files for each platform's native toolchain. Most modern C and C++ projects use CMake for exactly this reason.

---

## A Minimal CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.10)
project(MyApp)
add_executable(my_app main.cpp)
```

---

## Core Commands

```bash
cmake -S . -B build          # Configure: read CMakeLists.txt, generate build files in ./build
cmake --build build          # Build the project using the generated build files
cmake --build build --target clean   # Clean the build
ctest --test-dir build       # Run tests defined with CMake's testing support
```

`-S` points to the source directory, `-B` to the (usually separate) build directory — keeping build output out of your source tree is standard practice, often called an "out-of-source build."

---

## Generators

By default CMake picks a sensible generator for your platform (Makefiles on Linux, Visual Studio on Windows), but you can request a specific one:

```bash
cmake -S . -B build -G "Ninja"
```

---

## Example Walkthrough

```bash
cmake -S . -B build
cmake --build build
./build/my_app
```

Configures the project into a `build` folder, compiles it using the generated build system, then runs the resulting binary.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
