[Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[2]-Compiling-and-Running.md)

*Getting Started*

# Lesson 1 - Installing a Fortran Compiler (gfortran, ifx, flang)

Before you can run a single line of Fortran you need a **compiler**, the tool that turns your source code into a program your computer can execute. This lesson explains what a compiler is, shows how to install the three most common modern Fortran compilers, introduces the Fortran Package Manager (`fpm`), and ends with a quick check that everything works.

---

## 1.1 What Is a Compiler?

Computers only understand machine code: long sequences of binary instructions specific to a processor. Fortran source code is human-readable text, so it must be translated first. A **compiler** performs that translation.

```
 hello.f90  ──►  [ compiler ]  ──►  hello (executable)
 (source)                           (machine code)
```

Fortran is a **compiled language**: you translate the whole program once, then run the resulting executable as many times as you like. This is one reason Fortran programs are fast, because the compiler can spend time optimizing your math before the program ever runs.

Three compilers are used most often today:

| Compiler | Maintained by | Notes |
|----------|---------------|-------|
| `gfortran` | GNU / GCC project | Free, open source, available everywhere. **Used throughout this course.** |
| `ifx` | Intel | Free with the Intel oneAPI toolkit. Strong optimization on Intel hardware. Replaces the older `ifort`. |
| `flang` | LLVM project | Free, open source, built on LLVM. Newer and still maturing. |

All three implement the same language standard, so code that follows the standard compiles on any of them. A few **compiler flags** (options) differ, and those are covered in [Lesson 2](./[2]-Compiling-and-Running.md).

> **Which one should I pick?** If you are unsure, install `gfortran`. Every example in this course was written for it.

---

## 1.2 Installing gfortran (Linux / macOS / Windows)

`gfortran` is part of GCC, the GNU Compiler Collection. Installation depends on your operating system.

### Linux

Use your distribution's package manager:

```bash
# Debian, Ubuntu, Linux Mint
sudo apt update
sudo apt install gfortran

# Fedora, RHEL, CentOS Stream
sudo dnf install gcc-gfortran

# Arch Linux
sudo pacman -S gcc-fortran

# openSUSE
sudo zypper install gcc-fortran
```

### macOS

The easiest route is [Homebrew](https://brew.sh). The `gcc` formula includes `gfortran`:

```bash
brew install gcc
```

Homebrew installs versioned commands such as `gfortran-14` as well as a plain `gfortran` link. If `gfortran --version` is not found, check `ls $(brew --prefix)/bin | grep gfortran` to see the exact name.

### Windows

Windows has three practical options.

1. **MSYS2 (recommended for native programs).** Install MSYS2 from [msys2.org](https://www.msys2.org), open the *UCRT64* terminal, and run:
   ```bash
   pacman -S mingw-w64-ucrt-x86_64-gcc-fortran
   ```
2. **WSL (Windows Subsystem for Linux).** Install a Linux distribution such as Ubuntu from the Microsoft Store, then follow the Linux instructions above. This gives you a full Linux environment and is the closest match to how most scientific computing is done.
3. **Conda.** If you already use Anaconda or Miniforge, the same command works on all systems:
   ```bash
   conda install -c conda-forge gfortran
   ```

The GFortran project page ([gcc.gnu.org/wiki/GFortran](https://gcc.gnu.org/wiki/GFortran)) lists further binary sources.

---

## 1.3 Installing Intel `ifx` and LLVM `flang`

You only need these if you want to compare compilers or if your institution uses them. You can skip this section and return later.

### Intel `ifx`

`ifx` is part of the **Intel oneAPI HPC Toolkit**, a free download from [intel.com](https://www.intel.com/content/www/us/en/developer/tools/oneapi/hpc-toolkit.html). After installing, the compiler is not on your path automatically. You must load Intel's environment script first:

```bash
# Linux / macOS
source /opt/intel/oneapi/setvars.sh

# Windows (Command Prompt)
"C:\Program Files (x86)\Intel\oneAPI\setvars.bat"
```

The older compiler `ifort` is deprecated and was removed from oneAPI in 2025, so use `ifx` for new work.

### LLVM `flang`

`flang` is the Fortran front end of the LLVM project. Its command name changed over time: early builds were called `flang-new`, and current releases use `flang`. Options:

```bash
# Debian / Ubuntu (package name may include a version number)
sudo apt install flang

# Conda (all platforms)
conda install -c conda-forge flang
```

Pre-built releases are also available from [github.com/llvm/llvm-project/releases](https://github.com/llvm/llvm-project/releases).

---

## 1.4 Installing the Fortran Package Manager (`fpm`)

`fpm` is the community package manager and build tool for Fortran, similar to `cargo` for Rust or `npm` for JavaScript. It creates projects, works out the order in which files must be compiled, and builds and runs your program with simple commands. [Lesson 2](./[2]-Compiling-and-Running.md) uses it.

Installation options:

```bash
# Conda (all platforms)
conda install -c conda-forge fpm
```

You can also download a ready-made binary for your platform from the project's releases page, [github.com/fortran-lang/fpm/releases](https://github.com/fortran-lang/fpm/releases). Rename it to `fpm` (or `fpm.exe` on Windows), mark it executable on Linux and macOS (`chmod +x fpm`), and place it in a folder on your `PATH`. The full, up-to-date list of install methods is at [fpm.fortran-lang.org](https://fpm.fortran-lang.org).

`fpm` does not include a compiler. It calls `gfortran` (or another compiler you choose) behind the scenes, so install the compiler first.

---

## 1.5 Verifying Your Installation

Open a terminal and check the compiler version:

```bash
gfortran --version
```

You should see something like:

```
GNU Fortran (Ubuntu 13.3.0-6ubuntu2~24.04.1) 13.3.0
Copyright (C) 2023 Free Software Foundation, Inc.
```

Your version number will differ. Any version from 9 upward works for this course.

Now create a file named `hello.f90` containing:

```fortran
program hello
    implicit none
    print *, "Hello, World!"
end program hello
```

Compile and run it:

```bash
gfortran hello.f90 -o hello
./hello          # on Windows: hello.exe
```

Expected output:

```
 Hello, World!
```

(The leading space is normal. Fortran's default output reserves the first column for a legacy carriage-control character.)

If you installed `fpm`, check it too:

```bash
fpm --version
```

### Troubleshooting

| Symptom | Likely cause and fix |
|---------|----------------------|
| `gfortran: command not found` | The compiler is not installed or not on your `PATH`. Reopen the terminal, or on Windows make sure you are in the MSYS2 UCRT64 shell. |
| `./hello: Permission denied` | You are not in the folder containing the executable, or it was not created. Run `ls` and recompile. |
| `Error: Unclassifiable statement` | Check for typos in your source. A missing `end program` line is common. |
| `ifx: command not found` | The `setvars` script has not been run in this terminal session. |

---

[Table of Contents](./[0]-Introduction-to-Fortran.md) | [Next](./[2]-Compiling-and-Running.md)