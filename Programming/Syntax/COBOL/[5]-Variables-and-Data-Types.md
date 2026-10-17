[Previous]([4]-Program-Structure.md) | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([6]-Group-Items-and-Tables.md)

*Core Syntax*

# Lesson 5 - Variables & Data Types (`PICTURE` clauses, levels, `USAGE`)

COBOL does not have `int`, `float`, or `string` types. Instead, you describe the exact **shape** of every data item with a `PICTURE` clause, character by character. This is the foundation of the language, so it is worth learning carefully.

---

## 5.1 What Is a Data Item?

A **data item** is a named area of memory that holds a value. You declare it in the `DATA DIVISION` (usually in `WORKING-STORAGE SECTION`) with a **data description entry**:

```text
level-number  data-name  [PIC picture]  [USAGE usage]  [VALUE literal].
```

For example:

```cobol
01 WS-CUSTOMER-NAME   PIC X(20)     VALUE SPACES.
01 WS-AGE             PIC 9(3)      VALUE 0.
01 WS-BALANCE         PIC S9(7)V99  VALUE 0.
```

Three ideas to hold on to:

1. **Fixed size.** `PIC X(20)` always occupies exactly 20 characters. Shorter text is padded with spaces; longer text moved into it is truncated.
2. **The picture is the type.** The characters in the `PIC` tell the compiler whether the item is numeric, alphabetic, or alphanumeric, how many digits it holds, and where the decimal point is.
3. **Items are not automatically initialized** unless you give a `VALUE` (or use `INITIALIZE`). Without a `VALUE` the starting contents are compiler dependent. GnuCOBOL sets numeric fields to zero and alphanumeric fields to spaces by default, but IBM mainframes leave storage undefined. Never rely on it; initialize explicitly.

**Elementary and group items.** An item with a `PIC` is an **elementary item**. An item without one that is followed by subordinate items is a **group item** (Lesson 6).

---

## 5.2 Numeric, Alphabetic, and Alphanumeric `PIC` Clauses

### Picture symbols

| Symbol | Meaning | Example |
|---|---|---|
| `9` | One decimal digit | `PIC 999` holds 000 to 999 |
| `X` | Any character (letters, digits, symbols) | `PIC X(10)` holds up to 10 characters |
| `A` | A letter or space only | `PIC A(5)` |
| `S` | Operational sign (leading, takes no storage in the picture count) | `PIC S999` holds -999 to +999 |
| `V` | **Implied** decimal point (no storage used) | `PIC 9(3)V99` holds 123.45 |
| `P` | Scaling position (assumed zeros); rarely used | `PIC 99PPP` holds 12000 as `12` |
| `(n)` | Repeat count for the preceding symbol | `9(5)` is the same as `99999` |

**The decimal point is implied.** `PIC 9(3)V99` stores five digits, `12345`, and the program treats it as `123.45`. There is no `.` character in memory. This is how COBOL does exact decimal arithmetic (Lesson 9). To *show* a real decimal point you move the value to an edited picture (Lesson 8).

### The three basic categories

| Category | Picture | Allowed contents | Example |
|---|---|---|---|
| **Numeric** | `9`, `S`, `V` | Digits only (with optional sign) | `PIC S9(5)V99` |
| **Alphabetic** | `A` | Letters and spaces | `PIC A(15)` |
| **Alphanumeric** | `X` (or `A` mixed with `9`) | Anything | `PIC X(30)` |

Use `X` for names, addresses, codes, and anything that you will not do arithmetic on, **even if it contains digits** (phone numbers, ZIP codes, account numbers). Use `9` for quantities and money. In practice `PIC A` is rarely used because `X` is more forgiving.

### Size calculations

The size of a `DISPLAY` item is the number of picture characters, not counting `S` or `V`:

| Picture | Bytes | Range / content |
|---|---|---|
| `PIC 9(4)` | 4 | 0 to 9999 |
| `PIC S9(4)` | 4 | -9999 to +9999 |
| `PIC 9(5)V99` | 7 | 0.00 to 99999.99 |
| `PIC X(25)` | 25 | 25 characters |

### `USAGE`: how the value is stored

The optional `USAGE` clause controls the internal format. The default is `DISPLAY`: one byte per digit, stored as a character. Other forms are for efficiency:

| Usage | Also written | Storage | Used for |
|---|---|---|---|
| `DISPLAY` | (default) | 1 byte per digit/character | Data you display, read, and write as text |
| `COMP` / `BINARY` | `COMPUTATIONAL` | 2, 4, or 8 bytes binary integer | Counters, subscripts, fast integer math |
| `COMP-3` / `PACKED-DECIMAL` | `COMPUTATIONAL-3` | 2 digits per byte plus sign nibble | Money and decimals on mainframes; saves space |
| `COMP-5` | | Native binary | Interfacing with C code (Lesson 28) |
| `INDEX` | | Table position | Table indexes (Lesson 6) |

```cobol
01 WS-COUNTER   PIC 9(4)       USAGE COMP.
01 WS-PRICE     PIC S9(7)V99   COMP-3.
```

`USAGE` can be written with or without the word `USAGE`. Lesson 9.4 introduces `COMP-3` arithmetic and Lesson 22 covers all storage formats in detail. For now, use plain `DISPLAY` fields.

### A first look at data in action

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. DATATYPES.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-NAME        PIC X(12)     VALUE "Maria Santos".
01 WS-AGE         PIC 9(3)      VALUE 34.
01 WS-SALARY      PIC 9(5)V99   VALUE 2500.75.
01 WS-BALANCE     PIC S9(5)V99  VALUE -120.50.
01 WS-LETTERS     PIC A(5)      VALUE "ABCDE".
01 WS-SHORT       PIC X(5)      VALUE "ABC".
PROCEDURE DIVISION.
    DISPLAY "Name:    " WS-NAME
    DISPLAY "Age:     " WS-AGE
    DISPLAY "Salary:  " WS-SALARY
    DISPLAY "Balance: " WS-BALANCE
    DISPLAY "Letters: " WS-LETTERS
    DISPLAY "Short:   [" WS-SHORT "]"
    STOP RUN.
```

Output:

```text
Name:    Maria Santos
Age:     034
Salary:  02500.75
Balance: -00120.50
Letters: ABCDE
Short:   [ABC  ]
```

Notice:

- `WS-AGE` prints with its leading zero (`034`) because a `PIC 9(3)` always has three digits.
- `WS-SALARY` prints as `02500.75`. GnuCOBOL's `DISPLAY` inserts a decimal point at the implied position, but **no `.` is stored** in memory. IBM Enterprise COBOL prints the raw digits instead (`0250075`), so never depend on `DISPLAY` for formatted numbers. Use edited pictures (Lesson 8) when presentation matters.
- The signed `WS-BALANCE` shows a leading `-` in GnuCOBOL. On IBM compilers the sign is stored as an *overpunch* in the last digit, which looks odd when printed raw (for example `0012050` with the final `0` shown as `}`).
- `WS-SHORT` holds `ABC` followed by two padding spaces, because a `PIC X(5)` is always exactly five characters. A value that is too long is **truncated** on the right instead (see `MOVE` in Lesson 8).

---

## 5.3 Level Numbers (01, 05, 77, 88)

Level numbers say how data items relate to each other.

| Level | Meaning |
|---|---|
| `01` | Top-level item: a standalone variable or the name of a record. Must start in Area A in fixed-format |
| `02`-`49` | Subordinate items inside a group. A larger number is a deeper (more nested) item. Gaps like 01, 05, 10 are customary |
| `66` | `RENAMES`: an alternate name for a range of items (Lesson 6) |
| `77` | A standalone elementary item that is not part of any group (older style; `01` works just as well) |
| `88` | A **condition name**: a named true/false test on the item above it (Lesson 10) |

### Grouping with levels

```cobol
01 WS-EMPLOYEE.
   05 WS-EMP-ID        PIC 9(5).
   05 WS-EMP-NAME.
      10 WS-FIRST      PIC X(10).
      10 WS-LAST       PIC X(15).
   05 WS-EMP-SALARY    PIC 9(6)V99.
```

`WS-EMPLOYEE` and `WS-EMP-NAME` are **group items**; they have no `PIC` of their own. A group is always treated as alphanumeric text covering all its children, so `DISPLAY WS-EMPLOYEE` prints the whole record as one string. The other items are **elementary items**. Lesson 6 covers groups in depth.

### Level 77

```cobol
77 WS-COUNTER   PIC 9(3) VALUE 0.
```

A level-77 item cannot be subdivided and cannot be part of a group. It behaves exactly like a standalone `01` elementary item, and modern style simply uses `01`.

### Level 88: condition names (preview)

A level-88 entry attaches a readable name to specific values of the item above it:

```cobol
01 WS-STATUS-CODE   PIC X.
   88 STATUS-ACTIVE    VALUE "A".
   88 STATUS-CLOSED    VALUE "C".
```

You can then write `IF STATUS-ACTIVE` instead of `IF WS-STATUS-CODE = "A"`. Level 88 has no `PIC` and uses no storage. Lesson 10 covers it fully.

---

## 5.4 `VALUE`, Figurative Constants, and Constants

### The `VALUE` clause

`VALUE` sets the initial contents when the program starts:

```cobol
01 WS-RATE      PIC 9V999     VALUE 0.075.
01 WS-TITLE     PIC X(20)     VALUE "Monthly Report".
01 WS-LIMIT     PIC 9(4)      VALUE 1000.
```

Rules:

- Numeric items take numeric literals (no quotes). Alphanumeric items take **quoted** literals (either `"` or `'`).
- To include a quote mark inside a literal, double it: `"She said ""hi"""`.
- `VALUE` is not allowed on items inside the `FILE SECTION` or `LINKAGE SECTION` (except level 88).
- A group item can have a `VALUE`, but it initializes the whole group as alphanumeric text. Prefer to give `VALUE` to the elementary items.
- `VALUE` only sets the **initial** contents. Statements like `MOVE` change it later.

### Figurative constants

Figurative constants are reserved words that stand for common values. They fill the entire item, whatever its length:

| Constant | Meaning |
|---|---|
| `SPACE` / `SPACES` | Blank characters |
| `ZERO` / `ZEROS` / `ZEROES` | Zero (numeric zero, or `"0"` characters in alphanumeric items) |
| `HIGH-VALUE` / `HIGH-VALUES` | The highest character in the collating sequence (hex `FF`) |
| `LOW-VALUE` / `LOW-VALUES` | The lowest character (hex `00`) |
| `QUOTE` / `QUOTES` | The double-quote character |
| `ALL literal` | The literal repeated to fill the field, for example `ALL "-"` or `ALL "AB"` |

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. FIGURATIVE.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-LINE      PIC X(20) VALUE ALL "-".
01 WS-BLANK     PIC X(5)  VALUE SPACES.
01 WS-ZEROES    PIC 9(4)  VALUE ZERO.
01 WS-QUOTED    PIC X(10) VALUE "SAY ""HI""".
PROCEDURE DIVISION.
    DISPLAY WS-LINE
    DISPLAY "[" WS-BLANK "]"
    DISPLAY WS-ZEROES
    DISPLAY WS-QUOTED
    MOVE ALL "*" TO WS-LINE
    DISPLAY WS-LINE
    DISPLAY QUOTE "quoted" QUOTE
    STOP RUN.
```

Output:

```text
--------------------
[     ]
0000
SAY "HI"
********************
"quoted"
```

`HIGH-VALUES` is the standard "end of file" marker in match/merge programs (Lesson 18), because it compares greater than any real key.

### Constants

COBOL has no `const` keyword in the classic standard, so programmers use one of these approaches.

**1. A `VALUE`-initialized item** that the program never changes (works everywhere):

```cobol
01 WS-TAX-RATE    PIC V999 VALUE .075.
```

**2. The `CONSTANT` clause** (COBOL 2002 and later, supported by GnuCOBOL and recent IBM compilers). The compiler substitutes the value and refuses assignments to it:

```cobol
01 MAX-ROWS       CONSTANT AS 50.
01 APP-NAME       CONSTANT AS "PAYROLL".
```

**3. Level 78** (a Micro Focus and GnuCOBOL extension that predates `CONSTANT`):

```cobol
78 MAX-ROWS       VALUE 50.
```

A complete example of the `CONSTANT` clause:

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. CONSTS.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 MAX-ROWS      CONSTANT AS 50.
01 APP-NAME      CONSTANT AS "PAYROLL".
01 WS-ROWS-USED  PIC 99 VALUE 12.
PROCEDURE DIVISION.
    DISPLAY APP-NAME " uses " WS-ROWS-USED " of " MAX-ROWS " rows"
    STOP RUN.
```

```text
PAYROLL uses 12 of 50 rows
```

`INITIALIZE` is another way to set data. It resets numeric items to zero and alphanumeric items to spaces without writing `MOVE` for each one:

```cobol
INITIALIZE WS-EMPLOYEE.
```

---

## 5.5 Naming Conventions

### The rules (enforced by the compiler)

- A data name uses letters, digits, and hyphens.
- It must contain **at least one letter** (so `123` is not a name but `1ST-ITEM` is).
- It may not start or end with a hyphen.
- Standard COBOL limits names to 30 characters. GnuCOBOL accepts longer names, but staying within 30 keeps your code portable to mainframes.
- It cannot be a reserved word.
- Names are not case-sensitive, and the underscore is not allowed in standard COBOL (GnuCOBOL accepts it).

### Style conventions (followed by most teams)

| Convention | Example | Purpose |
|---|---|---|
| Hyphens between words | `CUSTOMER-BALANCE` | Readability; COBOL's equivalent of `snake_case` |
| `WS-` prefix | `WS-TOTAL` | Marks working-storage items |
| `LS-` prefix | `LS-COUNT` | Local-storage items |
| `LK-` or `LNK-` prefix | `LK-ACCOUNT-ID` | Linkage-section items |
| File record prefix | `CUST-NAME` | Distinguishes fields of different records |
| `-FLAG`, `-SW`, `-IND` suffix | `EOF-FLAG` | Yes/no switches (with level-88 names) |
| `-CNT`, `-TOT`, `-AMT` suffix | `ORDER-CNT` | Counters, totals, amounts |
| Numbered paragraph names | `2000-PROCESS-RECORDS` | Shows structure and execution order (Lesson 12) |

Prefer **meaningful names**: `WS-CUSTOMER-BALANCE` is far better than `WS-X1`. COBOL was designed to read almost like English, and long descriptive names are part of that tradition.

**Qualification.** The same name can appear in different records. In that case you disambiguate it with `OF` (or `IN`), as in `CUST-ID OF CUSTOMER-IN`. Unique prefixes make this unnecessary in most programs.

---

[Previous]([4]-Program-Structure.md) | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([6]-Group-Items-and-Tables.md)