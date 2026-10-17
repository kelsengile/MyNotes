[Previous]() | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([4]-Program-Structure.md)

*Getting Started*

# Lesson 3 - Your Development Environment (editors, debugging, VS Code extensions)

A good setup makes COBOL much more pleasant: syntax coloring, column rulers, quick compile commands, and a way to see what the program is doing when it misbehaves.

---

## 3.1 Choosing an Editor or IDE

COBOL is plain text, so any editor works. What matters is how well it handles the **column layout** of fixed-format code and how easily you can run the compiler.

| Option | Good for | Notes |
|---|---|---|
| **Visual Studio Code** | Most learners | Free, large extension ecosystem, built-in terminal |
| **Vim / Neovim / Emacs** | Terminal users | Emacs ships a `cobol-mode`; Vim has COBOL syntax files built in |
| **Notepad++ / Sublime Text / Kate** | Quick edits | Built-in COBOL highlighting; add a column ruler for fixed-format |
| **GnuCOBOL IDE (`gnucobol-ide`) / OpenCobolIDE** | Beginners who want a one-window tool | Community projects; availability and maintenance vary |
| **IBM Developer for z/OS / Z Open Editor** | Mainframe work | Connects to z/OS; needs access to a mainframe |
| **Eclipse Che for z / Rocket Visual COBOL IDE** | Commercial and enterprise teams | Commercial products |

**Editor settings that help with fixed-format COBOL**

- Use **spaces, not tabs**. A tab character would shift your code out of the correct columns.
- Show a **column ruler** at columns 7, 8, 12, and 72 so you can see Area A, Area B, and the right margin.
- Set the tab key to insert spaces up to the next stop (4 spaces is common).
- Use a **monospaced font**, so columns line up visually.

---

## 3.2 Syntax Highlighting and Extensions

If you use VS Code, install one or more of these from the Extensions panel (names can change over time, so search by keyword):

| Extension | What it provides |
|---|---|
| **COBOL** (publisher: bitlang) | Syntax highlighting, snippets, and basic navigation for many dialects |
| **SuperBOL Studio** (OCamlPro) | Language server for GnuCOBOL: diagnostics, completion, go-to-definition, and debugging support |
| **IBM Z Open Editor** | Language support for IBM Enterprise COBOL, copybook resolution, and mainframe integration |
| **Zowe Explorer** | Browse datasets and submit JCL on a mainframe (used later in the mainframe lessons) |

Useful editor features to turn on:

- **Rulers** in VS Code settings: `"editor.rulers": [7, 11, 72]`
- **Insert spaces**: `"editor.insertSpaces": true`
- **Trim trailing whitespace**: `"files.trimTrailingWhitespace": true`
- **File associations**: map `*.cob`, `*.cbl`, and `*.cpy` (copybooks) to the COBOL language mode.

### Compile from the editor

A VS Code task makes `Ctrl+Shift+B` compile the current file. Save this as `.vscode/tasks.json`:

```json
{
  "version": "2.0.0",
  "tasks": [
    {
      "label": "Compile COBOL (free-format)",
      "type": "shell",
      "command": "cobc",
      "args": ["-x", "-free", "-Wall", "-debug", "-o", "${fileDirname}/${fileBasenameNoExtension}", "${file}"],
      "group": { "kind": "build", "isDefault": true },
      "problemMatcher": {
        "owner": "cobol",
        "fileLocation": ["relative", "${workspaceFolder}"],
        "pattern": {
          "regexp": "^(.*):(\\\\d+):\\\\s+(error|warning):\\\\s+(.*)$",
          "file": 1, "line": 2, "severity": 3, "message": 4
        }
      }
    }
  ]
}
```

The `problemMatcher` turns `cobc` messages (such as `bad.cob:7: error: ...`) into clickable entries in the Problems panel. If your code is fixed-format, remove `"-free"` from the arguments.

---

## 3.3 Debugging with GnuCOBOL

There are four practical debugging techniques, from simplest to most powerful.

### 1. `DISPLAY` statements

Print values at key points. This is crude but effective, and you will use it constantly:

```cobol
DISPLAY "DEBUG: WS-TOTAL=" WS-TOTAL " WS-COUNT=" WS-COUNT
```

### 2. Debugging lines (`D` in column 7)

In fixed-format, a line with `D` in column 7 is compiled **only** when you ask for debugging. This lets you leave diagnostic code in the source without it running in production:

```cobol
      D    DISPLAY "DEBUG: reached 2000-PROCESS".
```

Compile with `-fdebugging-line` to include those lines (otherwise they are treated as comments). Free-format has an equivalent in `>>D` at the start of a line.

### 3. Runtime checks and tracing

```bash
cobc -x -free -debug myprog.cob          # subscript and reference-modification checks
cobc -x -free -ftrace myprog.cob         # lets READY TRACE print each paragraph entered
```

With `-debug`, bad subscripts stop the program with a message. `READY TRACE` and `RESET TRACE` (Lesson 21) switch tracing on and off inside the program.

### 4. A source-level debugger (`gdb`)

Because GnuCOBOL generates C, you can use the GNU debugger. Compile with `-g` and keep the generated C file with `-save-temps`:

```bash
cobc -x -free -g -save-temps myprog.cob
gdb ./myprog
```

Inside `gdb`, `break main`, `run`, and `next` step through the generated code. Editors that integrate with `gdb` (VS Code with a suitable extension, for instance) can show COBOL source lines and let you inspect data items. Debugger support is more limited than for C or Python, so most people rely on techniques 1 to 3 for day-to-day work.

> **Mainframe note.** On z/OS, the usual tools are IBM Debug Tool / IBM z/OS Debugger (interactive), the compiler options `TEST` and `SSRANGE` (similar in spirit to `-debug`), and formatted dump listings. You will meet them in Lessons 21 and 30.

---

## 3.4 A Recommended Project Layout

Keep source, copybooks, test data, and build output separate from the start. This layout scales from the first lesson to the projects at the end of the course:

```text
cobol-course/
├── src/          # .cob programs
├── copybooks/    # .cpy shared record layouts (Lesson 20)
├── data/         # sample input and output files
├── bin/          # compiled executables (do not commit)
├── tests/        # test programs and expected output
├── Makefile
└── .gitignore    # ignore bin/ and generated .c / .o files
```

A small `Makefile` compiles every program in `src/` into `bin/` and adds `run-<name>` shortcuts. Lines under a target **must start with a tab character**, not spaces:

```makefile
''' + mk + '''```

Usage:

```bash
make              # build everything in src/
make run-hello    # build (if needed) and run bin/hello
make clean
```

A matching `.gitignore`:

```text
bin/
*.o
*.so
*.c.h
*.c
```

**Naming tips**

- Use lowercase file names with a `.cob` or `.cbl` extension for programs and `.cpy` for copybooks.
- Name each program the same as its file (for example `payroll.cob` contains `PROGRAM-ID. PAYROLL.`).
- Keep one program per file unless you are deliberately learning about nested programs.

---

[Previous]() | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([4]-Program-Structure.md)