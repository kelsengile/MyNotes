[Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[2]-Compiling-and-Running.md)

*Getting Started*

# Lesson 1 - Installing A C Toolchain

## 1.1 What Is a Toolchain?

A C **toolchain** is the collection of programs that turns your source code (`.c` files) into a runnable program. At minimum, it includes:

- A **compiler** — translates C source code into machine code or an intermediate object file (e.g. `gcc`, `clang`, `cl.exe`).
- A **linker** — combines object files and libraries into a final executable.
- Often an **assembler** — converts compiler-generated assembly into machine code (usually invoked automatically by the compiler).

Unlike some languages, C has no single official implementation. The three toolchains you'll encounter most are **GCC** (GNU Compiler Collection), **Clang** (LLVM-based), and **MSVC** (Microsoft's compiler, bundled with Visual Studio).

---

## 1.2 Installing GCC (Linux / macOS)

**Linux (Debian/Ubuntu):**

```bash
sudo apt update
sudo apt install build-essential
```

`build-essential` pulls in GCC, `make`, and standard headers.

**Linux (Fedora):**

```bash
sudo dnf groupinstall "Development Tools"
```

**macOS:**

GCC on macOS is typically an alias for Clang unless you install the real thing via Homebrew:

```bash
brew install gcc
```

For most learning purposes, macOS's built-in Clang (installed via Xcode Command Line Tools, see 1.3) is sufficient.

---

## 1.3 Installing Clang

**Linux:**

```bash
sudo apt install clang
```

**macOS:**

```bash
xcode-select --install
```

This installs Apple's Command Line Tools, which includes Clang.

**Windows:**

Download prebuilt binaries from the [LLVM releases page](https://releases.llvm.org/), or install via a package manager like `winget install LLVM.LLVM`.

---

## 1.4 Installing MSVC (Windows)

MSVC is not a standalone download — it ships with **Visual Studio**:

1. Download Visual Studio Community (free) from Microsoft's website.
2. In the installer, select the **"Desktop development with C++"** workload. This includes the MSVC compiler, even though it's labeled for C++ — it compiles C as well.
3. Once installed, use the **"Developer Command Prompt for VS"** to access `cl.exe`, the MSVC compiler.

Alternatively, Windows users can skip MSVC entirely and use GCC via **MinGW-w64** or **WSL** (Windows Subsystem for Linux), which gives you a real Linux environment with `apt` and GCC.

---

## 1.5 Verifying Your Installation

After installing, confirm your compiler is available from the command line:

```bash
gcc --version
# or
clang --version
```

On Windows with MSVC, open the Developer Command Prompt and run:

```bash
cl
```

If you see version information instead of a "command not found" error, your toolchain is ready. The next lesson covers how to actually use it to compile and run a program.

---

[Table of Contents](./[0]-Introduction-to-C.md) | [Next](./[2]-Compiling-and-Running.md)
