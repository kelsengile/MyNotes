[Previous]([3]-Development-Environment.md) | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([5]-Variables-and-Data-Types.md)

*Core Syntax*

# Lesson 4 - Program Structure (the four divisions, columns, and layout rules)

Every COBOL program is organized into up to four **divisions**, always in the same order. This lesson shows what each one is for, then explains the column rules that surprise most newcomers.

---

## 4.1 IDENTIFICATION DIVISION

The first division names the program. Only `PROGRAM-ID` is required.

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. PAYROLL.
AUTHOR. Jane Developer.
DATE-WRITTEN. 2026-01-15.
```

- `PROGRAM-ID` gives the program its name. Other programs use this name in a `CALL` statement (Lesson 19).
- `AUTHOR`, `INSTALLATION`, `DATE-WRITTEN`, `DATE-COMPILED`, and `SECURITY` are optional documentation paragraphs. They are marked obsolete in recent standards but are still common in older code. Many modern shops use comments instead.
- The words `IDENTIFICATION DIVISION` can be abbreviated to `ID DIVISION`.

**Terminology.** COBOL source has a fixed hierarchy: **divisions** contain **sections**, which contain **paragraphs**, which contain **sentences**, which contain **statements**. A sentence ends with a period.

---

## 4.2 ENVIRONMENT DIVISION

This division describes the **outside world** the program depends on: the computer and the files. It is optional if the program uses no files and no special configuration.

```cobol
ENVIRONMENT DIVISION.
CONFIGURATION SECTION.
SPECIAL-NAMES.
    DECIMAL-POINT IS COMMA.        *> optional: use 1.234,56 style numbers
INPUT-OUTPUT SECTION.
FILE-CONTROL.
    SELECT CUSTOMER-FILE
        ASSIGN TO "customers.dat"
        ORGANIZATION IS LINE SEQUENTIAL
        FILE STATUS IS WS-FILE-STATUS.
```

| Section | Contents |
|---|---|
| `CONFIGURATION SECTION` | Source and object computer (obsolete), `SPECIAL-NAMES` (currency sign, decimal point, class definitions, device names) |
| `INPUT-OUTPUT SECTION` | `FILE-CONTROL` with a `SELECT` entry for every file, and `I-O-CONTROL` for rarely used tuning options |

The `SELECT` entry connects a **logical** file name used in the program to a **physical** file or dataset. That separation is why the same program can read a test file today and the production file tomorrow: only the assignment changes. File handling is covered in Lessons 14 and 15.

> **Mainframe note.** On z/OS, `ASSIGN TO CUSTFILE` names a **DD name**, and the JCL supplies the real dataset for that name (Lesson 31).

---

## 4.3 DATA DIVISION

The data division declares every piece of data the program uses. It has several sections, all optional:

| Section | Purpose |
|---|---|
| `FILE SECTION` | Record layouts for files named in `SELECT` (each described by an `FD` entry) |
| `WORKING-STORAGE SECTION` | Variables, constants, and tables owned by the program. Initialized when the program first starts |
| `LOCAL-STORAGE SECTION` | Like working-storage but re-initialized on every call (used with recursion and reentrant code) |
| `LINKAGE SECTION` | Describes data passed in from a calling program (Lesson 19) |
| `REPORT SECTION` | Report layouts for Report Writer (Lesson 16) |
| `SCREEN SECTION` | Screen layouts for interactive input (Lesson 7) |

A tiny working-storage example:

```cobol
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-CUSTOMER-NAME   PIC X(20) VALUE "ACME LTD".
01 WS-BALANCE         PIC 9(5)V99 VALUE 1250.75.
```

Each line is a **data description entry**: a level number (`01`), a name, a `PIC` (picture) that defines the type and size, and an optional `VALUE`. Lesson 5 explains these in depth.

---

## 4.4 PROCEDURE DIVISION

The procedure division holds the executable logic. Statements run from top to bottom unless you redirect them with `PERFORM`, `GO TO`, `CALL`, or conditional statements.

```cobol
PROCEDURE DIVISION.
MAIN-PARAGRAPH.
    DISPLAY "Starting".
    PERFORM SHOW-GREETING.
    STOP RUN.

SHOW-GREETING.
    DISPLAY "Hello from a paragraph".
```

Key points:

- A name starting in the paragraph position (Area A in fixed-format) begins a **paragraph**. Paragraphs are the labels you `PERFORM` (Lesson 12).
- `STOP RUN` ends the whole program. Without it, execution falls through into the next paragraph (here, `SHOW-GREETING` would run a second time).
- In a **subprogram**, use `GOBACK` instead, which returns to the caller. In a main program `GOBACK` behaves like `STOP RUN`.

### A complete program with all four divisions

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. ORDER-TOTAL.

ENVIRONMENT DIVISION.
CONFIGURATION SECTION.

DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-ITEM-NAME     PIC X(15) VALUE "Widget".
01 WS-QUANTITY      PIC 9(3)  VALUE 12.
01 WS-UNIT-PRICE    PIC 9(3)V99 VALUE 4.50.
01 WS-TOTAL         PIC 9(5)V99.
01 WS-TOTAL-EDITED  PIC $$,$$9.99.

PROCEDURE DIVISION.
MAIN-PARAGRAPH.
    COMPUTE WS-TOTAL = WS-QUANTITY * WS-UNIT-PRICE
    MOVE WS-TOTAL TO WS-TOTAL-EDITED
    DISPLAY "Item:  " WS-ITEM-NAME
    DISPLAY "Total: " WS-TOTAL-EDITED
    STOP RUN.
```

Expected output:

```text
Item:  Widget
Total:    $54.00
```

---

## 4.5 Columns, Areas A and B, and Comments

COBOL was born in the punched-card era, and fixed-format source still reflects an 80-column card. This is the layout used by virtually all mainframe code.

```text
         1         2         3         4         5         6         7
123456 7 8   12                                                    72 73    80
|SEQNO||I|A--||B-----------------------------------------------------||IDENT|
```

| Columns | Area | Rules |
|---|---|---|
| 1-6 | Sequence number | Ignored by the compiler |
| 7 | Indicator | `*` or `/` = comment, `-` = continuation, `D` = debug line, space = normal |
| 8-11 | Area A | Division headers, section headers, paragraph names, `FD`/`SD`, and level numbers `01` and `77` must **start** here |
| 12-72 | Area B | Statements, `PIC` clauses, continuation text, and level numbers `02` to `49` |
| 73-80 | Identification area | Ignored. Anything you type past column 72 is silently dropped |

The last row is a classic trap: a long statement that runs past column 72 is truncated without a warning, which can produce a confusing error on the next line.

### Fixed-format example

```cobol
      * This is a comment line (asterisk in column 7)
       IDENTIFICATION DIVISION.
       PROGRAM-ID. COLUMNS.
       DATA DIVISION.
       WORKING-STORAGE SECTION.
       01  WS-GREETING      PIC X(40)
               VALUE "A fixed-format program".
       PROCEDURE DIVISION.
       MAIN-PARA.
           DISPLAY WS-GREETING
           STOP RUN.
```

Look at where things start: `IDENTIFICATION`, `PROGRAM-ID`, `01`, and `MAIN-PARA.` begin in Area A (column 8). `DISPLAY` and the `VALUE` clause begin in Area B (column 12 or later).

### Continuation lines

A literal that does not fit on one line continues on the next line. In fixed-format, put a `-` in column 7 of the continuation line, then start the text with a quote:

```cobol
       01  WS-LONG-TEXT     PIC X(60) VALUE "This is a long literal that
      -    "continues on the next line".
```

For long literals the first line runs to column 72, and the continuation resumes with an opening quote. Many programmers avoid continuation entirely by using `STRING` (Lesson 13) or several shorter fields.

### Free-format rules

| Feature | Free-format |
|---|---|
| Columns | No restrictions; indent however you like |
| Comment | `*>` anywhere on the line, to the end of the line |
| Continuation | Not needed for statements. To build a long literal, join pieces with `&` (for example `"abc" & "def"`) |
| Directives | `>>SOURCE FORMAT IS FREE`, `>>SOURCE FORMAT IS FIXED`, `>>D` (debug line) |

### Periods, indentation, and style

- A period ends a **sentence**. It also silently ends every open `IF`, `EVALUATE`, or `PERFORM` inside that sentence. Modern style uses explicit terminators (`END-IF`, `END-PERFORM`, `END-EVALUATE`) and only places periods at the end of paragraphs and data entries. Lesson 10 shows why.
- COBOL is **case-insensitive**: `display`, `DISPLAY`, and `Display` are identical. Tradition is uppercase for reserved words, but lowercase or mixed case is fine.
- Spaces matter around arithmetic operators: write `A + B`, not `A+B`, because the scanner may otherwise read `A+B` as a single token.
- **Reserved words.** COBOL has hundreds of them (`DATE`, `TIME`, `COUNT`, `ORDER`, `NUMBER`, `FILE`, `RECORD`, and many others). Using one as a data name causes errors. The usual fix is a prefix such as `WS-`, so write `WS-COUNT` rather than `COUNT`.

> **IBM note.** Mainframe shops overwhelmingly use fixed-format with IBM Enterprise COBOL, so learn to read the column layout even if you write free-format at home.

---

[Previous]([3]-Development-Environment.md) | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([5]-Variables-and-Data-Types.md)