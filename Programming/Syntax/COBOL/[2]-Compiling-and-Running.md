[Previous]([1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([3]-Development-Environment.md)

*Getting Started*

# Lesson 2 - Compiling & Running Your First Program

This lesson walks through the whole edit, compile, run cycle with `cobc`, explains the two source layouts (free-format and fixed-format), and shows how to read compiler messages.

> **Convention used in this course.** Unless a sample says otherwise, code is written in **free-format** and compiled with `cobc -x -free`. Fixed-format samples are always labeled as such.

---

## 2.1 From Source Code to Executable

The path from text file to running program has these stages:

```text
hello.cob  --(cobc translates to C)-->  hello.c  --(gcc)-->  hello.o  --(link with libcob)-->  hello
```

1. **Source** is the `.cob` file you write.
2. **Translation and compilation.** `cobc` checks syntax, translates to C, and calls the C compiler.
3. **Linking.** The object code is linked with `libcob`, the COBOL runtime library.
4. **Execution.** You run the resulting file like any other program.

GnuCOBOL can produce two kinds of output:

| Kind | Flag | Result | Typical use |
|---|---|---|---|
| Executable | `-x` | `hello` (or `hello.exe`) | Main programs you run directly |
| Module | `-m` (default) | `hello.so` (or `.dll` / `.dylib`) | Subprograms loaded by `CALL` (Lesson 19) |

If you forget `-x`, `cobc` builds a module, which cannot be started from the command line.

---

## 2.2 Compiling with `cobc`

Create `hello.cob`:

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. HELLO.
PROCEDURE DIVISION.
    DISPLAY "Hello, World!".
    STOP RUN.
```

Compile and run:

```bash
cobc -x -free hello.cob     # produces ./hello
./hello
```

```text
Hello, World!
```

Useful variations:

```bash
cobc -x -free -o greet hello.cob    # choose the output name
cobc -x -free -j hello.cob          # compile, then run immediately
cobc -x -free main.cob util.cob     # several source files into one executable
```

On Windows the executable is `hello.exe` and you run it with `hello` or `.\hello`.

**Where does the program name come from?** The file name and the `PROGRAM-ID` do not have to match, but keeping them the same (`hello.cob` with `PROGRAM-ID. HELLO.`) avoids confusion. With `-x`, the output name defaults to the source file name without its extension.

**Return codes.** `STOP RUN` ends the program and returns control to the operating system. You can set the exit status with the special register `RETURN-CODE`:

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. EXITCODE.
PROCEDURE DIVISION.
    DISPLAY "Setting exit status 3".
    MOVE 3 TO RETURN-CODE.
    STOP RUN.
```

After running it on Linux or macOS, `echo $?` prints `3`. Batch schedulers and shell scripts use this value to decide whether a step succeeded. On the mainframe it becomes the job step's *condition code* (Lesson 31).

---

## 2.3 Free-Format vs Fixed-Format Source

COBOL was designed for punched cards, where each of the 80 columns had a defined meaning. Compilers still support that **fixed-format** layout, and it is what almost all mainframe code uses. Modern GnuCOBOL also accepts **free-format**, where position on the line does not matter.

### Fixed-format (the default for `cobc`)

| Columns | Name | Purpose |
|---|---|---|
| 1-6 | Sequence number area | Ignored (historically card numbers). May hold anything or be blank. |
| 7 | Indicator area | `*` comment line, `-` continuation, `D` debugging line, `/` comment plus page eject |
| 8-11 | **Area A** | Division, section, and paragraph headers, `01` and `77` level numbers |
| 12-72 | **Area B** | Statements and the rest of data descriptions |
| 73-80 | Identification area | Ignored |

The same program in fixed-format (each code line begins with seven spaces so the text starts in column 8):

```cobol
      * Fixed-format version of HELLO
       IDENTIFICATION DIVISION.
       PROGRAM-ID. HELLO.
       PROCEDURE DIVISION.
           DISPLAY "Hello, World!".
           STOP RUN.
```

Compile it without `-free`:

```bash
cobc -x hello-fixed.cob
```

### Free-format

In free-format there are no reserved columns. Comments start with `*>` and can appear anywhere on a line:

```cobol
*> Free-format version of HELLO
IDENTIFICATION DIVISION.
PROGRAM-ID. HELLO.
PROCEDURE DIVISION.
    DISPLAY "Hello, World!".   *> inline comment
    STOP RUN.
```

Compile with `-free`:

```bash
cobc -x -free hello.cob
```

You can also put the directive `>>SOURCE FORMAT IS FREE` as the first line of a file so that no flag is needed. Lesson 4 covers columns, Area A and Area B, and comments in more detail.

### Which should you use?

| Situation | Choice |
|---|---|
| Learning, new personal projects | Free-format (less fiddly) |
| Working on mainframe code or sharing code with mainframe teams | Fixed-format |
| Uncertain | Fixed-format is universally understood; free-format is a GnuCOBOL (and COBOL 2002+) convenience |

Mixing the two in one file is possible with the `>>SOURCE FORMAT` directive, but it is rarely a good idea.

---

## 2.4 Running and Reading Compiler Errors

The compiler reports problems as `file:line: severity: message`. Here is a program with a typo (`WS-COUNTER` instead of `WS-COUNT`):

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. BAD.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-COUNT PIC 9(3) VALUE 5.
PROCEDURE DIVISION.
    DISPLAY WS-COUNTER.
    STOP RUN.
```

Compiling it produces:

```text
bad.cob:7: error: 'WS-COUNTER' is not defined
```

Message wording differs slightly between versions, but the pattern is the same. Compiling a *free-format* file *without* `-free` produces a burst of errors about column 7:

```text
h.cob:1: error: invalid indicator 'F' at column 7
h.cob:2: error: invalid indicator 'M' at column 7
h.cob:6: error: PROGRAM-ID header missing
```

That "invalid indicator" message is the classic sign that you are using the wrong source format.

**Reading messages effectively**

- Fix the **first** error first. Later errors are often side effects of the first one.
- Messages come in levels: **warning** (the program compiled, but something looks suspect), **error** (no executable is produced), and **note** (extra information for a warning or error).
- Check the line *before* the reported line too. A missing period or a missing `END-IF` often shows up as an error on the following statement.
- The most frequent beginner errors are: a missing period after a paragraph header, a name that is misspelled or not defined, a reserved word used as a data name, and statements typed in the wrong columns (fixed-format).

**Runtime errors** happen after a successful compile. By default GnuCOBOL does not check subscripts or reference modification bounds. Compiling with `-debug` adds those checks, so an out-of-range subscript stops the program with a message instead of silently corrupting memory:

```bash
cobc -x -free -debug myprog.cob
```

Use `-debug` during development and leave it off for performance-sensitive production builds.

---

## 2.5 Common Compiler Flags

| Flag | Meaning |
|---|---|
| `-x` | Build an executable program |
| `-m` | Build a loadable module (default when `-x` is absent) |
| `-o <file>` | Name the output file |
| `-free` / `-fixed` | Select free-format or fixed-format source |
| `-j` | Run the program right after a successful build |
| `-Wall` | Enable most warnings. Use it often; it catches real mistakes |
| `-g` | Include C-level debugging information (for `gdb`) and stack checking |
| `-debug` | Add runtime checks (subscripts, reference modification, and others) |
| `-ftrace` | Generate code that supports `READY TRACE` (Lesson 21) |
| `-O`, `-O2` | Optimize the generated C code |
| `-I <dir>` | Add a folder to the search path for `COPY` members (Lesson 20) |
| `-std=<dialect>` | Choose a dialect: `default`, `cobol85`, `cobol2002`, `cobol2014`, `ibm`, `mf`, and others. `-std=ibm` makes GnuCOBOL behave more like IBM Enterprise COBOL |
| `-t <file>` | Write a program listing to a file |
| `-E` | Preprocess only (expand `COPY`, `REPLACE`) and stop |
| `-v` | Verbose: show the commands `cobc` runs |
| `--help` | List all options for your version |

A good default command for development is:

```bash
cobc -x -free -Wall -debug -o myprog myprog.cob
```

---

[Previous]([1]-Installation-and-Setup.md | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([3]-Development-Environment.md)