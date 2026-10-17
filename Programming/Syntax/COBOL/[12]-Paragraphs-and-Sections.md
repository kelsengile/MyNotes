[Previous]([11]-Loops.md) | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([13]-String-Handling.md)

*Core Syntax*

# Lesson 12 - Paragraphs, Sections & Structured Programming

A real COBOL program has hundreds or thousands of lines, so its logic must be divided into named pieces. Paragraphs and sections are those pieces, and `PERFORM` is how you call them. This lesson also explains why `GO TO` has a bad reputation and how to write programs that read from the top down.

---

## 12.1 Paragraphs and `PERFORM`

A **paragraph** is a named block of code in the `PROCEDURE DIVISION`. Its name starts in Area A (column 8 in fixed-format) and ends with a period. It contains sentences and statements until the next paragraph name (or section name, or end of the program).

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. PARAS.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-NUMBER   PIC 99 VALUE 7.
01 WS-DOUBLE   PIC 999.
PROCEDURE DIVISION.
MAIN-LOGIC.
    PERFORM SHOW-HEADER
    PERFORM CALCULATE
    PERFORM SHOW-RESULT
    STOP RUN.

SHOW-HEADER.
    DISPLAY "=== Doubler ===".

CALCULATE.
    COMPUTE WS-DOUBLE = WS-NUMBER * 2.

SHOW-RESULT.
    DISPLAY WS-NUMBER " doubled is " WS-DOUBLE.
```

```text
=== Doubler ===
07 doubled is 014
```

Key points:

- `PERFORM name` transfers control to the paragraph, runs it, and **returns to the statement after the `PERFORM`**. It works like calling a procedure with no parameters. The paragraphs share all the program's data.
- **Execution falls through.** If control reaches the end of a paragraph without a `PERFORM` or `GO TO`, it continues straight into the next paragraph. That is why `STOP RUN` (or `GOBACK`) at the end of `MAIN-LOGIC` is essential: without it, the program would run `SHOW-HEADER`, `CALCULATE`, and `SHOW-RESULT` a second time.
- The first paragraph in the procedure division is where execution begins.
- `PERFORM` can be combined with the loop forms from Lesson 11: `PERFORM PROCESS-RECORD UNTIL END-OF-FILE`.
- A paragraph can `PERFORM` other paragraphs, but it must not `PERFORM` itself, directly or through another paragraph. The standard does not allow a paragraph to be active twice at once, and the results are unpredictable.
- **A paragraph needs at least one sentence.** An empty paragraph needs the statement `CONTINUE` or `EXIT`.

### The `EXIT` paragraph

Older code ends a range of paragraphs with a paragraph containing only `EXIT`, used as a common target:

```cobol
2000-PROCESS-EXIT.
    EXIT.
```

`EXIT` does nothing. It is a marker for the end of a `PERFORM ... THRU` range or the target of a `GO TO`.

---

## 12.2 Sections and `PERFORM THRU`

A **section** is a group of paragraphs. It begins with a section name followed by the word `SECTION`:

```cobol
PROCEDURE DIVISION.
0000-MAIN SECTION.
    PERFORM 1000-INITIALIZE
    PERFORM 2000-PROCESS
    PERFORM 3000-FINISH
    STOP RUN.

1000-INITIALIZE SECTION.
    DISPLAY "Initializing".

2000-PROCESS SECTION.
    DISPLAY "Processing".

3000-FINISH SECTION.
    DISPLAY "Finishing".
```

`PERFORM 2000-PROCESS` on a section runs **all paragraphs in that section**, from its first line to the line before the next section name. That makes sections convenient to organize large programs: each section becomes a unit with its own helper paragraphs.

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. SECTIONS1.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-X    PIC 99 VALUE 5.
PROCEDURE DIVISION.
0000-MAIN SECTION.
    PERFORM 1000-REPORT
    STOP RUN.

1000-REPORT SECTION.
1000-START.
    DISPLAY "Report start"
    PERFORM 1100-DETAIL
    DISPLAY "Report end".

1100-DETAIL.
    DISPLAY "Detail line for " WS-X.
```

`1100-DETAIL` is a paragraph that belongs to section `1000-REPORT`. Run this program and the output is probably not what you expected:

```text
Report start
Detail line for 05
Report end
Detail line for 05
```

The detail line prints **twice**. `PERFORM 1100-DETAIL` runs it once, as intended. But `PERFORM 1000-REPORT` runs the *whole section*, so after `1000-START` finishes, execution falls straight into `1100-DETAIL` and runs it a second time before the section ends. This **fall-through** is the classic trap of mixing a section with paragraphs that are also performed individually.

Two ways to avoid it:

- Give every performed routine its own section, so each section holds exactly the statements that belong together.
- Or use only paragraphs (no sections) and let each `PERFORM` run exactly one paragraph.

### `PERFORM ... THRU`

`PERFORM first THRU last` runs every paragraph from `first` to `last` in the order they appear in the source:

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. THRU1.
PROCEDURE DIVISION.
MAIN-PARA.
    PERFORM STEP-A THRU STEP-C
    DISPLAY "Back in main"
    STOP RUN.

STEP-A.
    DISPLAY "Step A".

STEP-B.
    DISPLAY "Step B".

STEP-C.
    DISPLAY "Step C".
```

```text
Step A
Step B
Step C
Back in main
```

This relies on paragraph **order in the source**, so inserting a new paragraph in the middle of the range changes what runs. The convention that makes `THRU` safe is to give the range an exit paragraph:

```cobol
PERFORM 2000-PROCESS THRU 2000-PROCESS-EXIT
...
2000-PROCESS.
    IF NOT VALID-RECORD
        GO TO 2000-PROCESS-EXIT
    END-IF
    ... more statements ...
    .
2000-PROCESS-EXIT.
    EXIT.
```

Here the `GO TO` is a controlled jump to the end of the *same* range, which is the one use of `GO TO` that most shops still accept (see 12.3).

### Paragraph or section: which one?

| Feature | Paragraph | Section |
|---|---|---|
| Groups | Statements | Paragraphs |
| `PERFORM name` runs | That paragraph only | All its paragraphs |
| Needs `THRU` for ranges | Yes | Usually not |
| Used with `SORT` input/output procedures (Lesson 17) | Allowed | Common |
| Typical modern style | Preferred in small programs | Used in older and large programs |

Either works. Pick one style per program and be consistent. This course uses paragraphs, with numbered names.

---

## 12.3 `GO TO` and Why to Avoid It

`GO TO paragraph-name` jumps to a paragraph and **does not return**:

```cobol
GO TO 9000-ERROR-EXIT.
```

### Why `GO TO` causes problems

`PERFORM` always comes back, so you can follow the flow. `GO TO` can jump anywhere, so a program full of them becomes "spaghetti code": you cannot tell how you reached a line or where control goes next. Older COBOL programs are famous for this, and untangling them is a major part of maintaining legacy systems (Lesson 34).

### Related statements you may meet

| Statement | Effect | Advice |
|---|---|---|
| `GO TO para` | Unconditional jump | Avoid; allowed only to a range's exit paragraph |
| `GO TO p1 p2 p3 DEPENDING ON n` | Jump to the n-th paragraph (a computed `switch`) | Replace with `EVALUATE` |
| `ALTER para TO PROCEED TO other` | Changes where a `GO TO` goes **at run time** | Obsolete and very dangerous; never use it, but recognize it |
| `NEXT SENTENCE` | Skip to the statement after the next period | Replace with `CONTINUE` and `END-IF` |

### Better alternatives

| Instead of | Write |
|---|---|
| `GO TO` for a loop | `PERFORM UNTIL` or `PERFORM VARYING` |
| `GO TO` to skip to the end on an error | A nested `IF`/`EVALUATE`, or a status flag tested with level-88 names |
| `GO TO ... DEPENDING ON` | `EVALUATE` |
| `GO TO` leaving a loop | `EXIT PERFORM` |

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. NOGOTO.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-AGE     PIC 999 VALUE 17.
01 WS-OK-FLAG PIC X VALUE "Y".
   88 INPUT-OK    VALUE "Y".
   88 INPUT-BAD   VALUE "N".
PROCEDURE DIVISION.
MAIN-LOGIC.
    PERFORM VALIDATE-AGE
    IF INPUT-OK
        PERFORM PROCESS-AGE
    ELSE
        DISPLAY "Rejected: age out of range"
    END-IF
    STOP RUN.

VALIDATE-AGE.
    SET INPUT-OK TO TRUE
    IF WS-AGE < 18 OR WS-AGE > 120
        SET INPUT-BAD TO TRUE
    END-IF.

PROCESS-AGE.
    DISPLAY "Processing age " WS-AGE.
```

```text
Rejected: age out of range
```

---

## 12.4 Writing Clean, Top-Down Programs

**Structured programming** means building a program from a few simple, predictable shapes: sequence, selection (`IF`/`EVALUATE`), and iteration (`PERFORM`). Combined with paragraphs, it gives a program that can be read from the top down like an outline.

### The standard skeleton

Most batch programs follow the same three-part design:

```text
0000-MAIN
    PERFORM 1000-INITIALIZE       open files, set up, read first record
    PERFORM 2000-PROCESS          the main loop
        UNTIL END-OF-FILE
    PERFORM 3000-FINALIZE         totals, close files
    STOP RUN
```

Each of those paragraphs delegates to more specific ones, so every paragraph does one clear thing. A complete small example:

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. TOPDOWN.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-NUMBERS-DATA.
   05 FILLER           PIC 99 VALUE 12.
   05 FILLER           PIC 99 VALUE 7.
   05 FILLER           PIC 99 VALUE 30.
   05 FILLER           PIC 99 VALUE 5.
01 WS-NUMBERS REDEFINES WS-NUMBERS-DATA.
   05 WS-NUMBER        PIC 99 OCCURS 4 TIMES.
01 WS-INDEX            PIC 9 VALUE 0.
01 WS-COUNT            PIC 9 VALUE 4.
01 WS-TOTAL            PIC 9(4) VALUE 0.
01 WS-LARGEST          PIC 99 VALUE 0.
01 WS-END-FLAG         PIC X VALUE "N".
   88 END-OF-DATA          VALUE "Y".

PROCEDURE DIVISION.
0000-MAIN.
    PERFORM 1000-INITIALIZE
    PERFORM 2000-PROCESS-ITEM UNTIL END-OF-DATA
    PERFORM 3000-FINALIZE
    STOP RUN.

1000-INITIALIZE.
    DISPLAY "Starting"
    MOVE 0 TO WS-INDEX WS-TOTAL WS-LARGEST
    PERFORM 2900-NEXT-ITEM.

2000-PROCESS-ITEM.
    ADD WS-NUMBER(WS-INDEX) TO WS-TOTAL
    IF WS-NUMBER(WS-INDEX) > WS-LARGEST
        MOVE WS-NUMBER(WS-INDEX) TO WS-LARGEST
    END-IF
    PERFORM 2900-NEXT-ITEM.

2900-NEXT-ITEM.
    ADD 1 TO WS-INDEX
    IF WS-INDEX > WS-COUNT
        SET END-OF-DATA TO TRUE
    END-IF.

3000-FINALIZE.
    DISPLAY "Total:   " WS-TOTAL
    DISPLAY "Largest: " WS-LARGEST
    DISPLAY "Finished".
```

```text
Starting
Total:   0054
Largest: 30
Finished
```

### Guidelines for clean COBOL

1. **One purpose per paragraph.** If you cannot name it with a short verb phrase (`VALIDATE-ORDER`, `PRINT-TOTALS`), it is doing too much.
2. **Number paragraph names** (`1000-`, `2000-`, `2100-`) in the order they appear. A reader can then find any paragraph quickly, and the number tells you the nesting: `2100-` is called by `2000-`.
3. **The main paragraph reads like a table of contents.** It should contain only `PERFORM`s and `STOP RUN`, and the whole program's outline should fit on one screen.
4. **Terminate every conditional** with `END-IF`, `END-EVALUATE`, `END-PERFORM`. Put a period only at the end of a paragraph.
5. **Avoid `GO TO`**, `ALTER`, and `NEXT SENTENCE`.
6. **Use level-88 condition names** for flags and codes (Lesson 10).
7. **Do not let paragraphs fall through** by accident. End the main paragraph with `STOP RUN`, and place subordinate paragraphs after it.
8. **Keep paragraphs short**, ideally under one screen (about 25 to 50 lines).
9. **Comment the why, not the what.** `ADD 1 TO WS-COUNT` needs no comment; "skip trial accounts per ticket 1432" does.
10. **Use `GOBACK` instead of `STOP RUN`** in programs that are called by others, so the caller regains control (Lesson 19).

These habits matter far beyond style. COBOL programs commonly live for decades and are maintained by people who did not write them, so readability is a form of reliability.

---

[Previous]([11]-Loops.md) | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([13]-String-Handling.md)