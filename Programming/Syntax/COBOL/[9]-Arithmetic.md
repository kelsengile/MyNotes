[Previous]([8]-MOVE-and-Editing.md) | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([10]-Conditionals.md)

*Core Syntax*

# Lesson 9 - Arithmetic (`ADD`, `COMPUTE`, `ROUNDED`, `ON SIZE ERROR`)

COBOL's arithmetic is built for money. Numbers are stored as exact decimal digits, so `0.10 + 0.20` is exactly `0.30`, with none of the binary floating-point surprises found in other languages. This lesson covers the arithmetic statements, rounding, overflow handling, and the built-in math functions.

---

## 9.1 `ADD`, `SUBTRACT`, `MULTIPLY`, `DIVIDE`

Each verb has several forms. The key to reading them is to look for the word **`GIVING`**:

- **Without `GIVING`**, the result goes into (and replaces) the item after `TO`, `FROM`, or `BY`.
- **With `GIVING`**, the operands are left alone and the result goes into the item named after `GIVING`.

### `ADD`

```cobol
ADD 5 TO WS-COUNT                       *> WS-COUNT = WS-COUNT + 5
ADD WS-A WS-B TO WS-TOTAL               *> WS-TOTAL = WS-TOTAL + WS-A + WS-B
ADD WS-A WS-B GIVING WS-SUM             *> WS-SUM = WS-A + WS-B (A and B unchanged)
ADD 1 TO WS-X WS-Y WS-Z                 *> add 1 to each of three items
```

### `SUBTRACT`

```cobol
SUBTRACT 10 FROM WS-BALANCE             *> WS-BALANCE = WS-BALANCE - 10
SUBTRACT WS-A WS-B FROM WS-TOTAL        *> subtract A and B from TOTAL
SUBTRACT WS-COST FROM WS-PRICE GIVING WS-PROFIT   *> PROFIT = PRICE - COST
```

### `MULTIPLY`

```cobol
MULTIPLY 2 BY WS-QTY                    *> WS-QTY = WS-QTY * 2
MULTIPLY WS-QTY BY WS-PRICE GIVING WS-TOTAL   *> TOTAL = QTY * PRICE
```

### `DIVIDE`

```cobol
DIVIDE 4 INTO WS-AMOUNT                 *> WS-AMOUNT = WS-AMOUNT / 4
DIVIDE WS-TOTAL BY WS-COUNT GIVING WS-AVG       *> AVG = TOTAL / COUNT
DIVIDE WS-TOTAL BY 7 GIVING WS-WEEKS REMAINDER WS-DAYS   *> integer quotient and remainder
```

`INTO` and `BY` differ in operand order: `DIVIDE A INTO B` computes `B / A`, while `DIVIDE A BY B GIVING C` computes `A / B`.

### A working example

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. ARITH1.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-A        PIC 9(3) VALUE 100.
01 WS-B        PIC 9(3) VALUE 30.
01 WS-SUM      PIC 9(4).
01 WS-DIFF     PIC S9(4).
01 WS-PROD     PIC 9(6).
01 WS-QUOT     PIC 9(3).
01 WS-REM      PIC 9(3).
PROCEDURE DIVISION.
    ADD WS-A WS-B GIVING WS-SUM
    SUBTRACT WS-A FROM WS-B GIVING WS-DIFF
    MULTIPLY WS-A BY WS-B GIVING WS-PROD
    DIVIDE WS-A BY WS-B GIVING WS-QUOT REMAINDER WS-REM
    DISPLAY "Sum:        " WS-SUM
    DISPLAY "Difference: " WS-DIFF
    DISPLAY "Product:    " WS-PROD
    DISPLAY "Quotient:   " WS-QUOT " remainder " WS-REM
    STOP RUN.
```

```text
Sum:        0130
Difference: -0070
Product:    003000
Quotient:   003 remainder 010
```

The `DIVIDE` quotient is truncated to the size of `WS-QUOT` (no decimals), and the remainder holds what is left over.

---

## 9.2 `COMPUTE` and Expressions

`COMPUTE` evaluates a full arithmetic expression, much like an assignment statement in other languages:

```cobol
COMPUTE WS-TOTAL = WS-QTY * WS-PRICE
COMPUTE WS-AVG   = (WS-A + WS-B + WS-C) / 3
COMPUTE WS-AREA  = 3.14159 * WS-RADIUS ** 2
COMPUTE WS-NET   = WS-GROSS - (WS-GROSS * WS-TAX-RATE)
```

### Operators and precedence

| Operator | Meaning | Precedence |
|---|---|---|
| `**` | Exponentiation | Highest |
| unary `+` `-` | Sign | |
| `*` `/` | Multiply, divide | |
| `+` `-` | Add, subtract | Lowest |

Operators at the same level are evaluated left to right. Use parentheses to be explicit. **Put spaces around every operator** (`A * B`, not `A*B`) and around the hyphen in subtraction (`A - B`), because hyphens are also legal inside names and `A-B` is read as a single name.

### `COMPUTE` versus the verbs

| Use | When |
|---|---|
| `ADD 1 TO WS-COUNT` | Simple increments: shorter and clearer |
| `COMPUTE X = A * B + C` | Anything with more than one operation |

On the mainframe, `COMPUTE` with many operations may be compiled more slowly than the individual verbs, but readability usually matters more.

### Intermediate precision

COBOL carries **exact** intermediate results using as many decimal digits as necessary (the exact rules are compiler-specific), so you do not lose precision mid-expression. Only the final result is stored in the receiving item and truncated or rounded there.

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. EXACT.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-X       PIC 9V9   VALUE 0.1.
01 WS-Y       PIC 9V9   VALUE 0.2.
01 WS-SUM     PIC 9V99.
01 WS-THIRD   PIC 9V9(4).
PROCEDURE DIVISION.
    COMPUTE WS-SUM = WS-X + WS-Y
    COMPUTE WS-THIRD = 1 / 3
    DISPLAY "0.1 + 0.2 = " WS-SUM
    DISPLAY "1 / 3     = " WS-THIRD
    STOP RUN.
```

```text
0.1 + 0.2 = 0.30
1 / 3     = 0.3333
```

---

## 9.3 `ROUNDED` and `ON SIZE ERROR`

### Truncation by default

Without `ROUNDED`, extra decimal places are simply **cut off**:

```cobol
COMPUTE WS-RESULT = 10 / 3          *> PIC 9V99 stores 3.33
COMPUTE WS-RESULT = 20 / 3          *> PIC 9V99 stores 6.66 (not 6.67)
```

### `ROUNDED`

Adding `ROUNDED` after the receiving item rounds instead. The default mode rounds half **away from zero** (what most people expect):

```cobol
COMPUTE WS-RESULT ROUNDED = 20 / 3      *> stores 6.67
ADD WS-A TO WS-B ROUNDED
DIVIDE WS-TOTAL BY WS-COUNT GIVING WS-AVG ROUNDED
```

Newer standards (and GnuCOBOL and recent IBM compilers) also let you choose the mode: `ROUNDED MODE IS NEAREST-EVEN` (banker's rounding), `TRUNCATION`, `TOWARD-GREATER`, `TOWARD-LESSER`, `AWAY-FROM-ZERO`, `NEAREST-TOWARD-ZERO`, and `NEAREST-AWAY-FROM-ZERO` (the default).

### `ON SIZE ERROR`

A **size error** happens when the result does not fit in the receiving item (high-order digits would be lost) or when you divide by zero. By default the program **continues with a wrong value**, so for money this is dangerous.

Add `ON SIZE ERROR` to catch it. When it triggers, the receiving item is **left unchanged** and your handler runs instead:

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. SIZEERR.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-SMALL    PIC 99     VALUE 90.
01 WS-RESULT   PIC 9V99   VALUE 0.
01 WS-ZERO     PIC 9      VALUE 0.
01 WS-ROUNDED  PIC 9V99.
PROCEDURE DIVISION.
    ADD 20 TO WS-SMALL
        ON SIZE ERROR
            DISPLAY "Overflow! WS-SMALL stays " WS-SMALL
        NOT ON SIZE ERROR
            DISPLAY "Added OK: " WS-SMALL
    END-ADD

    DIVIDE 10 BY WS-ZERO GIVING WS-RESULT
        ON SIZE ERROR
            DISPLAY "Cannot divide by zero"
    END-DIVIDE

    COMPUTE WS-ROUNDED ROUNDED = 20 / 3
        ON SIZE ERROR
            DISPLAY "Too big"
        NOT ON SIZE ERROR
            DISPLAY "20 / 3 rounded = " WS-ROUNDED
    END-COMPUTE
    STOP RUN.
```

```text
Overflow! WS-SMALL stays 90
Cannot divide by zero
20 / 3 rounded = 6.67
```

Things to know:

- Every arithmetic verb has an **explicit scope terminator**: `END-ADD`, `END-SUBTRACT`, `END-MULTIPLY`, `END-DIVIDE`, `END-COMPUTE`. Use them whenever you use `ON SIZE ERROR`, so it is clear where the statement ends.
- `ROUNDED` goes right after the receiving item; `ON SIZE ERROR` goes after the whole operand list.
- Without `ON SIZE ERROR`, the result of an overflow is unreliable: some compilers truncate high-order digits, and on IBM systems the behavior can be affected by compiler options such as `TRUNC`.
- Check **before** dividing if you want to avoid the error: `IF WS-DIVISOR NOT = 0`.

---

## 9.4 Decimal Precision and `COMP-3`

### Exact decimal arithmetic

Because `PIC 9(5)V99` stores decimal digits, additions, subtractions, and multiplications of such fields are exact. Rounding happens only where you choose, and that is why financial systems trust COBOL. The largest numeric item allowed is 18 digits in standard COBOL (many compilers, including GnuCOBOL and IBM, allow up to 38 digits for intermediate results).

### Storage formats affect speed and size

A `DISPLAY` number uses a byte per digit, and the CPU must convert it before calculating. Two alternatives are common:

| Usage | Storage of 1234567 | Notes |
|---|---|---|
| `DISPLAY` | 7 bytes | Human-readable; slowest arithmetic |
| `COMP-3` (packed decimal) | 4 bytes | Two digits per byte plus a sign nibble; exact decimal; standard for money on mainframes |
| `COMP` / `BINARY` | 4 bytes | Fast for integers; not suited to large decimal money amounts on some platforms |

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. PACKED.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-DISPLAY-AMT   PIC S9(7)V99 VALUE 1234567.89.
01 WS-PACKED-AMT    PIC S9(7)V99 COMP-3 VALUE 1234567.89.
01 WS-BINARY-CNT    PIC 9(4) COMP VALUE 1000.
PROCEDURE DIVISION.
    ADD 0.11 TO WS-PACKED-AMT
    DISPLAY "Display bytes: " FUNCTION LENGTH(WS-DISPLAY-AMT)
    DISPLAY "Packed bytes:  " FUNCTION LENGTH(WS-PACKED-AMT)
    DISPLAY "Binary bytes:  " FUNCTION LENGTH(WS-BINARY-CNT)
    DISPLAY "Packed value:  " WS-PACKED-AMT
    STOP RUN.
```

```text
Display bytes: 9
Packed bytes:  5
Binary bytes:  2
Packed value:  +1234568.00
```

You use `COMP-3` items in `ADD`, `COMPUTE`, and `MOVE` exactly like display items. The compiler handles the conversion. `DISPLAY`ing a packed field converts it for you, but **writing** one to a text file would write unreadable packed bytes, so move it to a display or edited field first. Lesson 22 covers all storage formats.

**Rules of thumb**

- Money and quantities with decimals: `PIC S9(n)V99`, with `COMP-3` on mainframes.
- Counters and subscripts: `COMP` (binary).
- Anything shown or written as text: `DISPLAY` or an edited picture.

---

## 9.5 Intrinsic Functions

Intrinsic functions return a value and are called with the keyword `FUNCTION`. They can be used wherever a literal or data item is allowed, including in `COMPUTE` and `DISPLAY`:

```cobol
COMPUTE WS-ROOT = FUNCTION SQRT(WS-AREA)
DISPLAY FUNCTION MAX(WS-A WS-B WS-C)
```

### Common numeric functions

| Function | Result |
|---|---|
| `ABS(x)` | Absolute value |
| `SQRT(x)` | Square root |
| `MOD(a, b)` | Modulo, sign follows `b` (the divisor) |
| `REM(a, b)` | Remainder, sign follows `a` |
| `INTEGER(x)` | Greatest integer not greater than `x` (rounds toward minus infinity) |
| `INTEGER-PART(x)` | Integer part (truncates toward zero) |
| `FRACTION-PART(x)` | Digits after the decimal point |
| `MAX(a b c ...)`, `MIN(...)` | Largest, smallest argument |
| `SUM(a b c ...)` | Sum of arguments |
| `MEAN(...)`, `MEDIAN(...)`, `RANGE(...)` | Statistics over the arguments |
| `FACTORIAL(n)` | n! |
| `PI`, `E` | The constants (no parentheses) |
| `SIN`, `COS`, `TAN`, `ASIN`, `ACOS`, `ATAN`, `LOG`, `LOG10`, `EXP` | Trigonometry and logarithms |
| `RANDOM(seed)` | Pseudo-random number from 0 to 1 |

### Financial functions

| Function | Result |
|---|---|
| `ANNUITY(rate, periods)` | Ratio of a regular payment to the loan amount |
| `PRESENT-VALUE(rate, amount1 amount2 ...)` | Present value of a series of payments |

### Example

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. FUNCS.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-ROOT     PIC 9(3)V9(4).
01 WS-LOAN     PIC 9(7)V99 VALUE 10000.
01 WS-RATE     PIC V9(4)   VALUE .0050.
01 WS-PAYMENT  PIC 9(5)V99.
01 WS-NEG      PIC S9(3)   VALUE -7.
PROCEDURE DIVISION.
    COMPUTE WS-ROOT = FUNCTION SQRT(2)
    DISPLAY "SQRT(2)       = " WS-ROOT
    DISPLAY "ABS(-7)       = " FUNCTION ABS(WS-NEG)
    DISPLAY "MOD(17, 5)    = " FUNCTION MOD(17, 5)
    DISPLAY "MAX(4 9 2)    = " FUNCTION MAX(4 9 2)
    DISPLAY "INTEGER(-2.5) = " FUNCTION INTEGER(-2.5)
    DISPLAY "INT-PART(-2.5)= " FUNCTION INTEGER-PART(-2.5)
    COMPUTE WS-PAYMENT ROUNDED =
        WS-LOAN * FUNCTION ANNUITY(WS-RATE, 12)
    DISPLAY "Monthly payment on 10000 at 0.5%/month for 12 months: "
            WS-PAYMENT
    STOP RUN.
```

```text
SQRT(2)       = 001.4142
ABS(-7)       = +007
MOD(17, 5)    = 000000002
MAX(4 9 2)    = 9
INTEGER(-2.5) = -000000003
INT-PART(-2.5)= -000000002
Monthly payment on 10000 at 0.5%/month for 12 months: 00860.66
```

Notes:

- `DISPLAY` shows a function result in its raw internal form (for example `000000002` or `+007`). The exact padding depends on the compiler, so `MOVE` the result to an edited item (Lesson 8) when presentation matters.
- Arguments are separated by **spaces or commas**. Multi-argument lists such as `MAX(4 9 2)` use spaces.
- A function with no arguments, such as `PI`, is written without parentheses: `COMPUTE WS-C = 2 * FUNCTION PI * WS-R`.
- Function names and availability vary by compiler. GnuCOBOL implements nearly all of the COBOL 2014 set; IBM Enterprise COBOL supports a large, but different, subset. Check your compiler's reference when moving code.
- Date and string functions appear in Lesson 13 and Lesson 23.

---

[Previous]([8]-MOVE-and-Editing.md) | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([10]-Conditionals.md)