[Previous]() | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([9]-Arithmetic.md)

*Core Syntax*

# Lesson 8 - Moving & Editing Data (`MOVE`, edited pictures)

`MOVE` is the most common statement in COBOL. It copies data from one item to another and converts it to the receiving item's format as it goes. Edited pictures use that same conversion to turn raw numbers into readable, report-ready text.

---

## 8.1 The `MOVE` Statement

```text
MOVE source TO receiver [receiver ...]
```

The source can be a data item, literal, or figurative constant. The source is never changed. Each receiver gets a converted copy.

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. MOVES.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-A      PIC X(5).
01 WS-B      PIC X(5).
01 WS-C      PIC X(5).
01 WS-NUM    PIC 9(3) VALUE 42.
PROCEDURE DIVISION.
    MOVE "HELLO" TO WS-A
    MOVE WS-A TO WS-B WS-C            *> one source, two receivers
    MOVE SPACES TO WS-A               *> figurative constant
    MOVE WS-NUM TO WS-C               *> numeric into alphanumeric
    DISPLAY "A=[" WS-A "] B=[" WS-B "] C=[" WS-C "]"
    STOP RUN.
```

```text
A=[     ] B=[HELLO] C=[042  ]
```

### Categories and what is allowed

`MOVE` converts according to the **category** of the source and receiver (Lesson 5):

| Source \ Receiver | Alphanumeric | Numeric | Numeric-edited |
|---|---|---|---|
| **Alphanumeric** | Yes | Only if the source holds digits (an integer); avoid | Same caution |
| **Numeric (integer)** | Yes (treated as text of digits) | Yes | Yes |
| **Numeric (with decimals)** | **No** (compile error or undefined; move to an edited item or use `FUNCTION` instead) | Yes | Yes |
| **Numeric-edited** | Yes | Yes (de-editing, in most compilers) | Yes |

The most important rule to remember: **numeric moves align on the decimal point; alphanumeric moves align on the left.**

---

## 8.2 Truncation and Padding Rules

### Alphanumeric receivers: left-justified

- If the source is **shorter**, the extra positions on the right are filled with spaces.
- If the source is **longer**, the excess on the **right** is cut off (truncated) with no error.

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. ALPHAMOVE.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-LONG     PIC X(10) VALUE "ABCDEFGHIJ".
01 WS-SHORT    PIC X(4).
01 WS-TINY     PIC X(3) VALUE "AB".
01 WS-WIDE     PIC X(6).
PROCEDURE DIVISION.
    MOVE WS-LONG TO WS-SHORT
    MOVE WS-TINY TO WS-WIDE
    DISPLAY "[" WS-SHORT "]"
    DISPLAY "[" WS-WIDE "]"
    STOP RUN.
```

```text
[ABCD]
[AB    ]
```

Add `JUSTIFIED RIGHT` (or `JUST RIGHT`) to a receiver's description to right-align alphanumeric data instead:

```cobol
01 WS-RIGHT   PIC X(8) JUSTIFIED RIGHT.
```

### Numeric receivers: aligned on the decimal point

Digits are lined up on the decimal point. Missing positions are filled with **zeros**, and digits that do not fit are **truncated**:

- High-order (left) digits are lost if the integer part is too big.
- Low-order (right) digits are lost if the receiver has fewer decimal places (the value is **truncated, not rounded**).

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. NUMMOVE.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-SRC      PIC 9(4)V99 VALUE 1234.56.
01 WS-BIG      PIC 9(6)V9999.
01 WS-SMALLINT PIC 9(2)V99.
01 WS-NODEC    PIC 9(4).
PROCEDURE DIVISION.
    MOVE WS-SRC TO WS-BIG
    DISPLAY "Wider:        " WS-BIG
    MOVE WS-SRC TO WS-SMALLINT
    DISPLAY "Lost left:    " WS-SMALLINT
    MOVE WS-SRC TO WS-NODEC
    DISPLAY "Lost decimal: " WS-NODEC
    STOP RUN.
```

```text
Wider:        001234.5600
Lost left:    34.56
Lost decimal: 1234
```

Truncation is silent. If you need rounding or overflow detection, use `COMPUTE ... ROUNDED ... ON SIZE ERROR` (Lesson 9) instead of `MOVE`.

### Group moves are always alphanumeric

Moving a **group** item copies its bytes as text, character for character, regardless of the children's types. This is fast and common for copying whole records, but it does no numeric conversion, so source and receiver should have identical layouts.

### Signed and unsigned

Moving a negative value into an unsigned receiver stores its absolute value (the sign is lost). Declare the receiver with `S` if the sign matters.

### Initialization at a glance

| Goal | Statement |
|---|---|
| Blank a text field | `MOVE SPACES TO item` |
| Zero a number | `MOVE ZERO TO item` |
| Reset a whole record by type | `INITIALIZE record` |
| Fill with a character | `MOVE ALL "-" TO item` |

---

## 8.3 `CORRESPONDING`

`MOVE CORRESPONDING` (abbreviated `MOVE CORR`) copies every child of one group to the child of another group that has the **same name**, ignoring the others:

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. CORR.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 IN-REC.
   05 ID-NO     PIC 9(4)  VALUE 77.
   05 NAME      PIC X(8)  VALUE "DANIEL".
   05 SALARY    PIC 9(5)  VALUE 31000.
01 OUT-REC.
   05 NAME      PIC X(10).
   05 ID-NO     PIC 9(6).
   05 DEPT      PIC X(3)  VALUE "FIN".
PROCEDURE DIVISION.
    MOVE CORRESPONDING IN-REC TO OUT-REC
    DISPLAY "[" NAME OF OUT-REC "]"
    DISPLAY ID-NO OF OUT-REC
    DISPLAY DEPT
    STOP RUN.
```

```text
[DANIEL    ]
000077
FIN
```

Rules:

- Children are matched **by name** (and, if qualified groups nest, by qualified name). `SALARY` has no partner and `DEPT` has no source, so both are untouched.
- Each matched pair is moved as an ordinary elementary `MOVE`, so conversion and padding happen per field (note `ID-NO` grew from 4 to 6 digits).
- `ADD CORRESPONDING` and `SUBTRACT CORRESPONDING` work the same way for numbers.
- Use it where two layouts really do share names. Overuse hides which fields actually move, so many style guides discourage it.
- Because the same names appear in both groups, you qualify references with `OF` (`NAME OF OUT-REC`).

---

## 8.4 Edited Pictures (`$`, `,`, `.`, `Z`, `*`)

An **edited picture** formats a value for people to read. It is only for output: you cannot do reliable arithmetic *on* an edited item, so compute with ordinary numeric items and then `MOVE` the result to an edited item.

### Editing symbols

| Symbol | Effect |
|---|---|
| `9` | A digit position (always shows a digit) |
| `Z` | Digit position; replaces a **leading zero with a space** |
| `*` | Digit position; replaces a leading zero with an asterisk (check protection) |
| `.` | Real decimal point (a character in the output) |
| `,` | Inserted comma (suppressed along with leading zeros) |
| `$` | Currency sign. A single `$` is fixed; a **string** of `$` floats to sit just left of the first digit |
| `+` `-` | Sign. A single one is fixed; a string floats. `-` shows a minus only for negatives (blank otherwise); `+` shows `+` or `-` |
| `CR` `DB` | Shown after the number when it is negative (credit and debit), otherwise blanks |
| `B` | Inserts a blank |
| `0` | Inserts a zero |
| `/` | Inserts a slash (dates) |

### The effect on one value

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. EDITING.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-AMT        PIC S9(5)V99 VALUE 1234.56.
01 WS-NEG        PIC S9(5)V99 VALUE -42.50.
01 WS-ZERO       PIC S9(5)V99 VALUE ZERO.
01 E1  PIC 9(5).99.
01 E2  PIC ZZ,ZZ9.99.
01 E3  PIC $ZZ,ZZ9.99.
01 E4  PIC $$$,$$9.99.
01 E5  PIC **,**9.99.
01 E6  PIC ZZ,ZZ9.99-.
01 E7  PIC ZZ,ZZ9.99CR.
01 E8  PIC +ZZ,ZZ9.99.
01 E9  PIC ZZZZ9.99 BLANK WHEN ZERO.
01 WS-PHONE      PIC 9(10) VALUE 6325551234.
01 E-PHONE       PIC 999B999B9999.
01 WS-DATE       PIC 9(8)  VALUE 20261217.
01 E-DATE        PIC 9999/99/99.
PROCEDURE DIVISION.
    MOVE WS-AMT TO E1 DISPLAY "9(5).99      [" E1 "]"
    MOVE WS-AMT TO E2 DISPLAY "ZZ,ZZ9.99    [" E2 "]"
    MOVE WS-AMT TO E3 DISPLAY "$ZZ,ZZ9.99   [" E3 "]"
    MOVE WS-AMT TO E4 DISPLAY "$$$,$$9.99   [" E4 "]"
    MOVE WS-AMT TO E5 DISPLAY "**,**9.99    [" E5 "]"
    MOVE WS-NEG TO E6 DISPLAY "ZZ,ZZ9.99-   [" E6 "]"
    MOVE WS-NEG TO E7 DISPLAY "ZZ,ZZ9.99CR  [" E7 "]"
    MOVE WS-AMT TO E8 DISPLAY "+ZZ,ZZ9.99   [" E8 "]"
    MOVE WS-ZERO TO E9 DISPLAY "BLANK WHEN ZERO[" E9 "]"
    MOVE WS-PHONE TO E-PHONE DISPLAY "Phone [" E-PHONE "]"
    MOVE WS-DATE TO E-DATE DISPLAY "Date  [" E-DATE "]"
    STOP RUN.
```

Output:

```text
9(5).99      [01234.56]
ZZ,ZZ9.99    [ 1,234.56]
$ZZ,ZZ9.99   [$ 1,234.56]
$$$,$$9.99   [ $1,234.56]
**,**9.99    [*1,234.56]
ZZ,ZZ9.99-   [    42.50-]
ZZ,ZZ9.99CR  [    42.50CR]
+ZZ,ZZ9.99   [+ 1,234.56]
BLANK WHEN ZERO[        ]
Phone [632 555 1234]
Date  [2026/12/17]
```

Study the differences:

- `Z` leaves **spaces** where leading zeros would be. A fixed `$` followed by `Z`s leaves a gap (`$ 1,234.56`).
- A **floating** string of `$` removes the gap: the `$` slides right until it is next to the first significant digit. (In a floating string, the first `$` is only the sign position, so `$$$,$$9.99` shows at most 5 integer digits.)
- `*` fills the unused leading positions, which is how checks are printed so that nobody can add a digit.
- Trailing `-` and `CR` appear only when the value is negative.
- `BLANK WHEN ZERO` shows all spaces for a zero value.
- Insertion characters `B`, `/`, and `0` add presentation characters to otherwise plain digits.

### How big must the edited picture be?

Count every symbol (digit positions, the `.`, commas, the sign, `CR`/`DB`). That is the size in bytes. Make it big enough for the largest value, because high-order digits that do not fit are lost silently.

### Edited fields for output, plain fields for math

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. INVOICE.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-QTY         PIC 9(3)    VALUE 24.
01 WS-PRICE       PIC 9(4)V99 VALUE 19.99.
01 WS-LINE-TOTAL  PIC 9(6)V99.
01 WS-QTY-ED      PIC ZZ9.
01 WS-PRICE-ED    PIC $$,$$9.99.
01 WS-TOTAL-ED    PIC $$$$,$$9.99.
PROCEDURE DIVISION.
    COMPUTE WS-LINE-TOTAL = WS-QTY * WS-PRICE
    MOVE WS-QTY        TO WS-QTY-ED
    MOVE WS-PRICE      TO WS-PRICE-ED
    MOVE WS-LINE-TOTAL TO WS-TOTAL-ED
    DISPLAY "Qty " WS-QTY-ED " @ " WS-PRICE-ED " = " WS-TOTAL-ED
    STOP RUN.
```

```text
Qty  24 @    $19.99 =     $479.76
```

### De-editing

Moving an edited item to a plain numeric item removes the editing characters and converts the text back into a number. If you need to turn arbitrary text such as `"1,234.50"` into a number, use `FUNCTION NUMVAL` or `FUNCTION NUMVAL-C` (Lesson 13), which are safer than relying on de-editing moves.

### Currency sign and decimal point

The currency symbol and decimal point can be changed in `SPECIAL-NAMES` (Lesson 4):

```cobol
SPECIAL-NAMES.
    CURRENCY SIGN IS "E"
    DECIMAL-POINT IS COMMA.
```

With `DECIMAL-POINT IS COMMA`, the roles reverse: `.` becomes the thousands separator and `,` the decimal point (`ZZ.ZZ9,99`), and numeric literals are written `12,50`.

---

[Previous]() | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([9]-Arithmetic.md)