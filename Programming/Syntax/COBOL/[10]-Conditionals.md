[Previous]() | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([11]-Loops.md)

*Core Syntax*

# Lesson 10 - Conditionals (`IF`, `EVALUATE`, condition names)

Programs make decisions with `IF` and `EVALUATE`. COBOL adds a few features you will not find elsewhere: class and sign conditions, abbreviated comparisons, and level-88 **condition names** that give tests readable names.

---

## 10.1 `IF`, `ELSE`, and `END-IF`

```text
IF condition
    statements
[ELSE
    statements]
END-IF
```

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. GRADES.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-SCORE    PIC 999 VALUE 82.
PROCEDURE DIVISION.
    IF WS-SCORE >= 90
        DISPLAY "Grade A"
    ELSE
        IF WS-SCORE >= 80
            DISPLAY "Grade B"
        ELSE
            DISPLAY "Grade C or lower"
        END-IF
    END-IF
    STOP RUN.
```

```text
Grade B
```

### Always use `END-IF`

`END-IF` is the **scope terminator** that closes an `IF`. The older style ended an `IF` with a period, and a period closes **every** open `IF` at once. That causes subtle bugs:

```cobol
*> OLD STYLE (dangerous): the period ends BOTH ifs
IF WS-A > 0
    IF WS-B > 0
        DISPLAY "both positive".
DISPLAY "this always runs".
```

Modern style: use `END-IF` for every `IF`, and put a period only at the end of a paragraph.

### Other pieces

- **`THEN`** is optional: `IF WS-A > 5 THEN ...`.
- **`CONTINUE`** is a do-nothing statement, useful as a placeholder: `IF WS-OK CONTINUE ELSE PERFORM ERROR-ROUTINE END-IF`.
- **`NEXT SENTENCE`** jumps to the statement after the next period. It exists in older code, but it is easily misused. Prefer `CONTINUE` with `END-IF`.
- An `IF` may contain any number of statements in each branch. No braces are needed.

---

## 10.2 Relational, Class, and Sign Conditions

### Relational conditions (comparisons)

| Symbols | Words | Meaning |
|---|---|---|
| `=` | `EQUAL TO` | Equal |
| `NOT =` | `NOT EQUAL TO` | Not equal (there is no `<>` or `!=` in the standard) |
| `>` | `GREATER THAN` | Greater |
| `<` | `LESS THAN` | Less |
| `>=` | `GREATER THAN OR EQUAL TO` | Greater or equal |
| `<=` | `LESS THAN OR EQUAL TO` | Less or equal |

How comparison works depends on the types:

- **Numeric vs numeric**: compared by algebraic value, so `PIC 9(3)` `007` equals `PIC 9(5)V99` `7.00`.
- **Alphanumeric vs alphanumeric**: compared **character by character** using the collating sequence (ASCII or EBCDIC, Lesson 24). A shorter item is padded with spaces for the comparison, so `"AB"` equals `"AB   "`. That is also why `"A" < "a"` in ASCII.
- Comparing a numeric item to a non-numeric literal is a common source of surprises. Compare like with like.

### Compound conditions

Combine tests with `AND`, `OR`, and `NOT`. `NOT` binds tightest, then `AND`, then `OR`. Use parentheses to be clear:

```cobol
IF WS-AGE >= 18 AND WS-AGE < 65
    DISPLAY "Working age"
END-IF

IF (WS-STATE = "CA" OR WS-STATE = "NY") AND WS-TOTAL > 1000
    PERFORM APPLY-DISCOUNT
END-IF
```

### Abbreviated conditions

COBOL lets you omit a repeated subject and operator:

```cobol
IF WS-AGE >= 18 AND < 65             *> same as: WS-AGE >= 18 AND WS-AGE < 65
IF WS-CODE = "A" OR "B" OR "C"       *> same as three separate comparisons
```

Use abbreviation sparingly, since it can read ambiguously when `NOT` is involved.

### Class conditions

A class condition tests what **kind of characters** an item contains:

| Test | True when the item contains |
|---|---|
| `item IS NUMERIC` | Only digits (valid for a numeric picture; for `PIC X` it means all digits) |
| `item IS ALPHABETIC` | Only letters and spaces |
| `item IS ALPHABETIC-LOWER` | Only lowercase letters and spaces |
| `item IS ALPHABETIC-UPPER` | Only uppercase letters and spaces |
| `item IS NOT NUMERIC` | Anything else |

`IS NUMERIC` is essential for validating input before doing arithmetic. Bad data in a numeric field is a leading cause of runtime failures (abends) in production batch jobs:

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. VALIDATE.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-INPUT-1   PIC X(5) VALUE "12345".
01 WS-INPUT-2   PIC X(5) VALUE "12A45".
01 WS-NAME      PIC X(8) VALUE "Anna".
PROCEDURE DIVISION.
    IF WS-INPUT-1 IS NUMERIC
        DISPLAY WS-INPUT-1 " is numeric"
    END-IF
    IF WS-INPUT-2 IS NOT NUMERIC
        DISPLAY WS-INPUT-2 " is NOT numeric"
    END-IF
    IF WS-NAME IS ALPHABETIC
        DISPLAY "Name is alphabetic"
    END-IF
    STOP RUN.
```

```text
12345 is numeric
12A45 is NOT numeric
Name is alphabetic
```

You can also define your own class in `SPECIAL-NAMES` (for example `CLASS VOWEL IS "AEIOU"`) and test `IF WS-CHAR IS VOWEL`.

### Sign conditions

```cobol
IF WS-BALANCE IS POSITIVE ...
IF WS-BALANCE IS NEGATIVE ...
IF WS-BALANCE IS ZERO ...          *> also valid: IF WS-BALANCE = ZERO
```

`POSITIVE` means greater than zero (zero is neither positive nor negative).

---

## 10.3 Level-88 Condition Names

A **condition name** is a level-88 entry that sits under a data item and names one or more of its values. The condition is *true* when the parent item holds one of those values.

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. COND88.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-STATUS       PIC X VALUE "A".
   88 STATUS-ACTIVE     VALUE "A".
   88 STATUS-CLOSED     VALUE "C".
   88 STATUS-VALID      VALUE "A" "C" "S".
01 WS-AGE          PIC 999 VALUE 25.
   88 AGE-MINOR         VALUE 0 THRU 17.
   88 AGE-ADULT         VALUE 18 THRU 64.
   88 AGE-SENIOR        VALUE 65 THRU 120.
01 WS-EOF-FLAG     PIC X VALUE "N".
   88 END-OF-FILE       VALUE "Y".
   88 MORE-DATA         VALUE "N".
PROCEDURE DIVISION.
    IF STATUS-ACTIVE
        DISPLAY "Account is active"
    END-IF
    IF NOT STATUS-CLOSED
        DISPLAY "Account is not closed"
    END-IF
    IF STATUS-VALID
        DISPLAY "Status code is valid"
    END-IF
    IF AGE-ADULT
        DISPLAY "Adult"
    END-IF

    SET STATUS-CLOSED TO TRUE           *> moves "C" into WS-STATUS
    DISPLAY "Status is now " WS-STATUS
    SET END-OF-FILE TO TRUE
    IF END-OF-FILE
        DISPLAY "Reached end"
    END-IF
    STOP RUN.
```

```text
Account is active
Account is not closed
Status code is valid
Adult
Status is now C
Reached end
```

Why use them:

- The reader sees **meaning** (`IF END-OF-FILE`) instead of **encoding** (`IF WS-EOF-FLAG = "Y"`).
- The codes live in one place. If `"Y"` ever changes, only the definition changes.
- `SET name TO TRUE` makes the parent hold the first value of that condition, without repeating the literal.
- A condition name takes **no storage** and cannot be used as a normal variable.
- A condition can list several values (`VALUE "A" "C" "S"`) and ranges (`VALUE 0 THRU 17`). Use `SET ... TO TRUE` only with a condition that has a (first) single value; for a range it uses the first value of the range.
- `SET name TO FALSE` works only if you specify a `WHEN SET TO FALSE IS value` clause in newer COBOL.

This is the idiom you will see in nearly every professional program, especially for end-of-file switches (Lesson 14).

---

## 10.4 `EVALUATE` (the COBOL `switch`)

`EVALUATE` selects one of many branches. It is a more powerful form of `switch` or `case`: it can test ranges, conditions, and several subjects at once.

### Simple form: one subject

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. EVAL1.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-DAY    PIC 9 VALUE 6.
PROCEDURE DIVISION.
    EVALUATE WS-DAY
        WHEN 1 THRU 5
            DISPLAY "Weekday"
        WHEN 6
        WHEN 7
            DISPLAY "Weekend"
        WHEN OTHER
            DISPLAY "Invalid day"
    END-EVALUATE
    STOP RUN.
```

```text
Weekend
```

Only the **first** matching `WHEN` runs, and then control leaves the `EVALUATE`. There is no fall-through and no `break` statement. Two `WHEN`s stacked with no statements between them (`WHEN 6` and `WHEN 7` above) share one action. `WHEN OTHER` is the default branch.

### `EVALUATE TRUE`: ordered conditions

When each branch has its own full condition, evaluate against `TRUE`. This replaces long `IF / ELSE IF` chains:

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. EVAL2.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-SCORE    PIC 999 VALUE 73.
01 WS-GRADE    PIC X.
PROCEDURE DIVISION.
    EVALUATE TRUE
        WHEN WS-SCORE >= 90
            MOVE "A" TO WS-GRADE
        WHEN WS-SCORE >= 80
            MOVE "B" TO WS-GRADE
        WHEN WS-SCORE >= 70
            MOVE "C" TO WS-GRADE
        WHEN OTHER
            MOVE "F" TO WS-GRADE
    END-EVALUATE
    DISPLAY "Score " WS-SCORE " is grade " WS-GRADE
    STOP RUN.
```

```text
Score 073 is grade C
```

### Several subjects with `ALSO`

You can test two or more subjects together. Each `WHEN` lists one value per subject, separated by `ALSO`. Use `ANY` to ignore a subject:

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. EVAL3.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-MEMBER   PIC X VALUE "Y".
01 WS-TOTAL    PIC 9(4) VALUE 600.
01 WS-DISCOUNT PIC 99 VALUE 0.
PROCEDURE DIVISION.
    EVALUATE WS-MEMBER ALSO TRUE
        WHEN "Y" ALSO WS-TOTAL >= 500
            MOVE 15 TO WS-DISCOUNT
        WHEN "Y" ALSO ANY
            MOVE 10 TO WS-DISCOUNT
        WHEN OTHER
            MOVE 0 TO WS-DISCOUNT
    END-EVALUATE
    DISPLAY "Discount: " WS-DISCOUNT "%"
    STOP RUN.
```

```text
Discount: 15%
```

### `EVALUATE` with condition names

Combining `EVALUATE` with level-88 names gives very readable code:

```cobol
EVALUATE TRUE
    WHEN STATUS-ACTIVE
        PERFORM PROCESS-ACTIVE
    WHEN STATUS-CLOSED
        PERFORM PROCESS-CLOSED
    WHEN OTHER
        PERFORM REPORT-BAD-STATUS
END-EVALUATE
```

### Choosing between `IF` and `EVALUATE`

| Situation | Use |
|---|---|
| One test, with or without an else branch | `IF` |
| Three or more mutually exclusive branches | `EVALUATE` |
| Branching on a code with many possible values | `EVALUATE subject` |
| Decision tables on two or more fields | `EVALUATE ... ALSO ...` |

Always end it with `END-EVALUATE`, and always include a `WHEN OTHER` so unexpected values are handled instead of silently ignored.

---

[Previous]() | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([11]-Loops.md)