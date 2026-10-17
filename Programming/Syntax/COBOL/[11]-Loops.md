[Previous]([10]-Conditionals.md) | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([12]-Paragraphs-and-Sections.md)

*Core Syntax*

# Lesson 11 - Loops (`PERFORM`, `UNTIL`, `VARYING`)

COBOL has no `for` or `while` keyword. Every kind of loop, and every call to a paragraph, is a form of the single verb `PERFORM`. Once you know its variants, you know all of COBOL's iteration.

---

## 11.1 Inline `PERFORM`

An **inline** `PERFORM` places the loop body right between `PERFORM` and `END-PERFORM`. With no loop clause it runs the body **once**, which is rarely useful on its own, but it is the starting point for every loop form that follows:

```cobol
PERFORM
    DISPLAY "runs once"
END-PERFORM
```

There are two styles of `PERFORM` in general:

| Style | Looks like | Use for |
|---|---|---|
| **Inline** | `PERFORM ... END-PERFORM` with statements inside | Short loop bodies |
| **Out-of-line** | `PERFORM paragraph-name ...` | Larger bodies and reusable logic (Lesson 12) |

Every loop clause in the sections below (`TIMES`, `UNTIL`, `VARYING`) works with both styles. For example:

```cobol
PERFORM PRINT-HEADING                       *> out-of-line, once
PERFORM PRINT-LINE 5 TIMES                  *> out-of-line, repeated
PERFORM 5 TIMES                             *> inline, repeated
    DISPLAY "hello"
END-PERFORM
```

---

## 11.2 `PERFORM UNTIL`

`PERFORM UNTIL condition` repeats the body until the condition becomes **true**. This is a "while not" loop: the loop continues while the condition is *false*.

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. UNTIL1.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-COUNT    PIC 99 VALUE 1.
PROCEDURE DIVISION.
    PERFORM UNTIL WS-COUNT > 5
        DISPLAY "Count = " WS-COUNT
        ADD 1 TO WS-COUNT
    END-PERFORM
    DISPLAY "Done, count is " WS-COUNT
    STOP RUN.
```

```text
Count = 01
Count = 02
Count = 03
Count = 04
Count = 05
Done, count is 06
```

### `TEST BEFORE` and `TEST AFTER`

By default the condition is checked **before** each pass (`TEST BEFORE`, like `while`). If it is already true, the body never runs. `WITH TEST AFTER` checks it **after** each pass, so the body always runs at least once (like `do ... while`):

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. TESTAFTER.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-N       PIC 99 VALUE 10.
PROCEDURE DIVISION.
    PERFORM WITH TEST BEFORE UNTIL WS-N > 5
        DISPLAY "before: body ran"
    END-PERFORM
    PERFORM WITH TEST AFTER UNTIL WS-N > 5
        DISPLAY "after: body ran once"
    END-PERFORM
    STOP RUN.
```

```text
after: body ran once
```

### The classic input loop

Reading a file until it ends uses `PERFORM UNTIL` with a level-88 end-of-file flag (the full pattern appears in Lesson 14):

```cobol
PERFORM UNTIL END-OF-FILE
    PERFORM PROCESS-RECORD
    PERFORM READ-NEXT-RECORD
END-PERFORM
```

### Infinite loops

If nothing in the body can make the condition true, the program never ends. Make sure the body changes something the condition depends on. If you do need a loop that exits from the middle, use `EXIT PERFORM` (see 11.5).

---

## 11.3 `PERFORM VARYING`

`PERFORM VARYING` is COBOL's counting loop. It names a counter, a starting value, a step, and an end condition:

```text
PERFORM VARYING counter FROM start BY step UNTIL condition
    body
END-PERFORM
```

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. VARY1.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-I       PIC 99.
01 WS-SQUARE  PIC 9(4).
PROCEDURE DIVISION.
    PERFORM VARYING WS-I FROM 1 BY 1 UNTIL WS-I > 5
        COMPUTE WS-SQUARE = WS-I * WS-I
        DISPLAY WS-I " squared is " WS-SQUARE
    END-PERFORM
    STOP RUN.
```

```text
01 squared is 0001
02 squared is 0004
03 squared is 0009
04 squared is 0016
05 squared is 0025
```

How it works:

1. `WS-I` is set to the `FROM` value.
2. The `UNTIL` condition is tested. If true, the loop ends.
3. The body runs.
4. `WS-I` is increased by the `BY` value, then step 2 repeats.

Notes:

- The condition says when to **stop**, not when to continue. To count from 1 to 5, you stop when the counter is `> 5`.
- Counting down uses a negative step: `PERFORM VARYING WS-I FROM 5 BY -1 UNTIL WS-I < 1`.
- After the loop, the counter holds the first value that made the condition true (here 6). Size the counter so it can hold that final value. For example, a `PIC 9` counter in a loop that stops at `> 9` can never reach 10, so the condition never becomes true and the loop runs forever.
- With indexed tables, you can vary an index directly: `PERFORM VARYING DEPT-IDX FROM 1 BY 1 UNTIL DEPT-IDX > 4` (Lesson 6).
- `COMP` counters are faster than `DISPLAY` counters on most platforms.

### Walking a table

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. VARY2.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-NAMES.
   05 WS-NAME     PIC X(6) OCCURS 4 TIMES.
01 WS-I           PIC 9 COMP.
PROCEDURE DIVISION.
    MOVE "ALBERT" TO WS-NAME(1)
    MOVE "BEATRI" TO WS-NAME(2)
    MOVE "CARLOS" TO WS-NAME(3)
    MOVE "DIANA " TO WS-NAME(4)
    PERFORM VARYING WS-I FROM 1 BY 1 UNTIL WS-I > 4
        DISPLAY WS-I ": " WS-NAME(WS-I)
    END-PERFORM
    STOP RUN.
```

```text
1: ALBERT
2: BEATRI
3: CARLOS
4: DIANA 
```

---

## 11.4 `PERFORM n TIMES`

When you only need a fixed number of repetitions, and no counter, use `TIMES`. The count can be a literal or a numeric data item:

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. TIMES1.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-REPEAT  PIC 9 VALUE 3.
PROCEDURE DIVISION.
    PERFORM 2 TIMES
        DISPLAY "Hip hip"
    END-PERFORM
    PERFORM WS-REPEAT TIMES
        DISPLAY "Hooray"
    END-PERFORM
    STOP RUN.
```

```text
Hip hip
Hip hip
Hooray
Hooray
Hooray
```

If the count is zero or negative the body does not run. The count is evaluated **once**, when the loop starts, so changing `WS-REPEAT` inside the body does not change how many passes happen.

---

## 11.5 Nested Loops

### Nested `PERFORM VARYING`

Loops can be placed inside each other to any depth. Keep an eye on `END-PERFORM` terminators so each belongs to the loop you intend:

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. TIMESTABLE.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-ROW      PIC 99.
01 WS-COL      PIC 99.
01 WS-PRODUCT  PIC ZZ9.
PROCEDURE DIVISION.
    PERFORM VARYING WS-ROW FROM 1 BY 1 UNTIL WS-ROW > 3
        PERFORM VARYING WS-COL FROM 1 BY 1 UNTIL WS-COL > 4
            COMPUTE WS-PRODUCT = WS-ROW * WS-COL
            DISPLAY WS-PRODUCT WITH NO ADVANCING
        END-PERFORM
        DISPLAY " "
    END-PERFORM
    STOP RUN.
```

```text
  1  2  3  4 
  2  4  6  8 
  3  6  9 12 
```

### `VARYING ... AFTER` (a built-in nested loop)

`AFTER` adds an inner counter to the same statement. The **first** counter is the outer loop and the **last** one changes fastest:

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. AFTER1.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-R   PIC 9.
01 WS-C   PIC 9.
PROCEDURE DIVISION.
    PERFORM VARYING WS-R FROM 1 BY 1 UNTIL WS-R > 2
            AFTER WS-C FROM 1 BY 1 UNTIL WS-C > 3
        DISPLAY "R=" WS-R " C=" WS-C
    END-PERFORM
    STOP RUN.
```

```text
R=1 C=1
R=1 C=2
R=1 C=3
R=2 C=1
R=2 C=2
R=2 C=3
```

This form is common for processing two-dimensional tables (Lesson 6). Explicit nested loops are usually easier to read.

### Leaving a loop early: `EXIT PERFORM`

`EXIT PERFORM` leaves the **innermost inline** `PERFORM` immediately. `EXIT PERFORM CYCLE` skips the rest of the body and goes to the next pass.

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. EXITLOOP.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-I     PIC 99.
PROCEDURE DIVISION.
    PERFORM VARYING WS-I FROM 1 BY 1 UNTIL WS-I > 10
        IF WS-I = 3
            EXIT PERFORM CYCLE
        END-IF
        IF WS-I = 6
            EXIT PERFORM
        END-IF
        DISPLAY "Pass " WS-I
    END-PERFORM
    DISPLAY "Stopped at " WS-I
    STOP RUN.
```

```text
Pass 01
Pass 02
Pass 04
Pass 05
Stopped at 06
```

> `EXIT PERFORM` is part of the 2002 standard. GnuCOBOL and newer IBM compilers support it. Older mainframe code achieves the same effect by setting a flag that the `UNTIL` condition tests.

### Summary of `PERFORM` forms

| Form | Meaning |
|---|---|
| `PERFORM para` | Run a paragraph once |
| `PERFORM para-a THRU para-b` | Run a range of paragraphs once |
| `PERFORM para n TIMES` | Repeat a paragraph n times |
| `PERFORM para UNTIL cond` | Repeat until a condition is true |
| `PERFORM para VARYING i FROM a BY b UNTIL cond` | Counting loop over a paragraph |
| `PERFORM ... END-PERFORM` | The same forms, with the body written inline |

---

[Previous]([10]-Conditionals.md) | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([12]-Paragraphs-and-Sections.md)