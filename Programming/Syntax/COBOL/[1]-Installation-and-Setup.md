
[Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([2]-Compiling-and-Running.md)

*Getting Started*

# Lesson 1 - Installing a COBOL Compiler (GnuCOBOL, IBM Enterprise COBOL)

Before you can write COBOL you need a way to turn source text into a program the computer can run. This lesson explains what that tool is, how to install it on your own machine, what alternatives exist, and how to confirm everything works.

---

## 1.1 What Is a COBOL Compiler?

A **compiler** translates human-readable source code into something a machine can execute. A COBOL source file (usually ending in `.cob`, `.cbl`, or `.cobol`) is plain text. The compiler reads it, checks it against the language rules, and produces an executable (or an intermediate object file).

Two compilers matter for this course:

| Compiler | Where it runs | Cost | Used for |
|---|---|---|---|
| **GnuCOBOL** | Linux, macOS, Windows, BSD | Free, open source | Learning, small tools, migration projects |
| **IBM Enterprise COBOL for z/OS** | IBM mainframes | Commercial | Production banking, insurance, government |

Other commercial compilers exist (for example Rocket Visual COBOL, formerly Micro Focus), but if you know GnuCOBOL and the mainframe differences covered later, you can move to any of them.

**How GnuCOBOL works internally.** GnuCOBOL does not generate machine code directly. The `cobc` command translates your COBOL into C, then calls a C compiler (usually GCC) to produce the executable. A small runtime library (`libcob`) supplies COBOL behaviors such as decimal arithmetic and file handling. This is why a working C compiler must be present on your system.

**A note on dialects.** COBOL has been standardized several times (COBOL-74, COBOL-85, 2002, 2014, 2023). Compilers add their own extensions. This course teaches standard COBOL first and calls out GnuCOBOL-specific or IBM-specific behavior when it differs.

---

## 1.2 Installing GnuCOBOL (Linux / macOS / Windows)

Official download page: <https://gnucobol.sourceforge.io/>

### Linux

Most distributions package GnuCOBOL. The package manager also installs a C compiler dependency for you.

```bash
# Debian / Ubuntu / Linux Mint
sudo apt update
sudo apt install gnucobol

# Fedora / RHEL family
sudo dnf install gnucobol

# Arch Linux
sudo pacman -S gnucobol
```

Distribution packages are sometimes a version or two behind. If you need the newest release, download the source tarball from the official page and build it:

```bash
tar xf gnucobol-3.2.tar.gz
cd gnucobol-3.2
./configure
make
sudo make install
sudo ldconfig
```

You need `gcc`, `make`, and development libraries for GMP, ncurses, and (for indexed files) Berkeley DB or a similar library. The `README` and `INSTALL` files in the tarball list the exact requirements.

### macOS

Use [Homebrew](https://brew.sh/):

```bash
brew install gnucobol
```

Homebrew also installs the dependencies. You need the Xcode command line tools for the C compiler (`xcode-select --install`).

### Windows

You have three realistic choices:

1. **WSL (Windows Subsystem for Linux)** is the easiest and most reliable option. Install WSL with an Ubuntu distribution, open the Ubuntu terminal, and follow the Linux steps above.
2. **Pre-built Windows binaries.** The GnuCOBOL project and its community publish Windows builds that bundle a C compiler. Follow the links from the official download page and read the notes that come with the build. After installing, use the provided command prompt (or add the `bin` folder to your `PATH`) so that `cobc` can be found.
3. **MSYS2 or a Docker container**, if you already use those tools.

---

## 1.3 Using Online Compilers and Docker

### Online compilers

If you just want to try a snippet, web-based compilers let you paste COBOL code and run it without installing anything. Examples include OneCompiler and JDoodle. These are fine for the first few lessons, but they typically offer limited file access, no real stdin handling, and older GnuCOBOL versions, so you will outgrow them when you reach the file-processing lessons (Lesson 14 onward).

### Docker

Docker gives you a throwaway Linux environment with a known GnuCOBOL version, without touching your main system. A minimal `Dockerfile`:

```dockerfile
FROM ubuntu:24.04
RUN apt-get update && apt-get install -y gnucobol && rm -rf /var/lib/apt/lists/*
WORKDIR /work
CMD ["bash"]
```

Build the image and run it with your current folder mounted at `/work`:

```bash
docker build -t cobol-lab .
docker run --rm -it -v "$PWD":/work cobol-lab
```

Inside the container you can run `cobc` exactly as you would on a normal Linux machine, and your source files stay on your own disk.

---

## 1.4 Mainframe Access Options (IBM Z Xplore)

Real-world COBOL mostly runs on IBM Z mainframes under **z/OS**, using **IBM Enterprise COBOL**. You cannot install that on a laptop, but you can still get hands-on experience:

- **IBM Z Xplore** is IBM's free learning program. After you register with an IBM account, you get access to a real z/OS system through a terminal and ISPF, along with guided challenges. It is the best way to try JCL, datasets, and CICS (covered in the mainframe lessons later in the course).
- **IBM Z Open Development / IBM Developer for z/OS** are IDEs that connect to a mainframe and give you modern editing, debugging, and syntax checking.
- **Hercules** is an open-source emulator that can run very old public-domain mainframe operating systems. It is useful for history and curiosity, but the COBOL compilers available for those systems are decades old and differ noticeably from modern Enterprise COBOL.

Most of what you learn with GnuCOBOL transfers directly. The main differences are in the surrounding environment (JCL instead of a shell command, datasets instead of files, EBCDIC instead of ASCII) rather than in the language core. Each lesson that touches such a difference includes a short note.

---

## 1.5 Verifying Your Installation

Open a terminal and ask the compiler for its version:

```bash
cobc --version
```

You should see something like this (your version number may differ):

```text
cobc (GnuCOBOL) 3.1.2.0
Copyright (C) 2020 Free Software Foundation, Inc.
...
```

Next, create a file named `hello.cob` with this program:

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. HELLO.
PROCEDURE DIVISION.
    DISPLAY "Hello, World!".
    STOP RUN.
```

Compile and run it. The `-free` flag tells the compiler this file uses free-format layout (explained in Lesson 2):

```bash
cobc -x -free hello.cob
./hello
```

Expected output:

```text
Hello, World!
```

If you see that, your toolchain works.

**Troubleshooting checklist**

| Symptom | Likely cause and fix |
|---|---|
| `cobc: command not found` | The install did not finish or `cobc` is not on your `PATH`. Reopen the terminal, or add the install folder to `PATH`. |
| Errors about `gcc` or `cc` not found | No C compiler is installed. Install `build-essential` (Debian/Ubuntu), the Xcode command line tools (macOS), or the compiler bundled with your Windows build. |
| `error while loading shared libraries: libcob.so` when running | The runtime library is not on the loader path. On Linux run `sudo ldconfig`, or set `LD_LIBRARY_PATH` to the folder containing `libcob`. |
| Compiler complains about the very first line | You compiled a free-format file without `-free`. Add `-free`, or move your code to the fixed-format columns (Lesson 2 and Lesson 4). |

---

[Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([2]-Compiling-and-Running.md)