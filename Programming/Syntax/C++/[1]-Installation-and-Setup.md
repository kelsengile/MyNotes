[Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[2]-Compiling-and-Running.md)

*Getting Started*

# Lesson 1 - Installation And Setup

## 1.1 Why You Need A Toolchain

C++ is a **compiled language**: the code you write in a `.cpp` file isn't run directly. It has to be translated into machine code your computer can execute. The set of tools that does this translation is called a **toolchain**, and it usually includes:

- A **compiler** (turns your source code into object code)
- A **linker** (combines object code and libraries into a final program)
- Sometimes a **standard library implementation** bundled alongside

The three most common toolchains are **GCC**, **Clang**, and **MSVC**. They all support standard C++, so code written for one will generally work with the others, though each has its own extra features and quirks.

---

## 1.2 Installing GCC (Linux/macOS)

**GCC** (the GNU Compiler Collection) is the default compiler on most Linux distributions.

On Debian/Ubuntu-based systems:

```bash
sudo apt update
sudo apt install build-essential
```

On Fedora:

```bash
sudo dnf install gcc-c++
```

On macOS, GCC is often aliased to Clang by default, but you can install a real GCC via [Homebrew](https://brew.sh):

```bash
brew install gcc
```

---

## 1.3 Installing Clang

**Clang** is the compiler from the LLVM project, known for fast compile times and detailed error messages.

On Debian/Ubuntu:

```bash
sudo apt install clang
```

On macOS, Clang ships with the **Xcode Command Line Tools**:

```bash
xcode-select --install
```

On Windows, Clang can be installed standalone or as part of LLVM's official installer from [releases.llvm.org](https://releases.llvm.org).

---

## 1.4 Installing MSVC (Windows)

**MSVC** (Microsoft Visual C++) is the compiler bundled with **Visual Studio** on Windows. It integrates tightly with Windows-specific APIs and debugging tools.

1. Download **Visual Studio Community** (free) from [visualstudio.microsoft.com](https://visualstudio.microsoft.com).
2. During installation, select the **"Desktop development with C++"** workload.
3. This installs the MSVC compiler (`cl.exe`), the Windows SDK, and the **Developer Command Prompt**, which sets up your environment variables automatically.

Windows users who prefer GCC or Clang can also use **MSYS2** or **WSL (Windows Subsystem for Linux)** to get a Linux-style toolchain.

---

## 1.5 Verifying Your Installation

Once installed, confirm the compiler is available from your terminal:

```bash
g++ --version      # GCC
clang++ --version  # Clang
cl                 # MSVC (from a Developer Command Prompt)
```

If a version number prints out, you're ready to compile your first program in the next lesson.

[Table of Contents](./[0]-Introduction-to-C++.md) | [Next](./[2]-Compiling-and-Running.md)
