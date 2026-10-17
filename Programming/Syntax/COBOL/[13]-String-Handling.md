[Previous]([12]-Paragraphs-and-Sections.md) | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([14]-Sequential-Files.md)

*Core Syntax*

# Lesson 13 - String Handling (`STRING`, `UNSTRING`, `INSPECT`)

COBOL strings are fixed-length character fields, so string handling is about working with positions and lengths. This lesson covers the tools for slicing, joining, splitting, counting, and replacing text, plus the most useful intrinsic functions.

---

## 13.1 Reference Modification

**Reference modification** selects a part of an alphanumeric item by position and length:

```text
item(start : length)
```

- `start` is the position of the first character (the first character is **1**).
- `length` is how many characters to take. If you omit it, the selection runs to the end of the item.
- Both can be literals or numeric data items. Because it is a **section of the item itself**, it can be used on either side of a `MOVE`.

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. REFMOD.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-TEXT     PIC X(12) VALUE "COBOL ROCKS!".
01 WS-WORD     PIC X(5).
01 WS-POS      PIC 99 VALUE 7.
PROCEDURE DIVISION.
    MOVE WS-TEXT(1:5) TO WS-WORD
    DISPLAY "First word:  " WS-WORD
    DISPLAY "From pos 7:  " WS-TEXT(WS-POS:)
    DISPLAY "Last char:   " WS-TEXT(12:1)
    MOVE "cobol" TO WS-TEXT(1:5)         *> overwrite part of the item
    DISPLAY "Modified:    " WS-TEXT
    STOP RUN.
```

```text
First word:  COBOL
From pos 7:  ROCKS!
Last char:   !
Modified:    cobol ROCKS!
```

### Tips

- Reference modification works on `PIC X` items and on `DISPLAY` numeric items (treated as text), but not on packed or binary numbers.
- It can be combined with subscripts: `WS-NAME(3)(1:2)` means "the first two characters of the third table entry".
- The compiler does not check that `start` and `length` stay inside the item unless you compile with `-debug`. An out-of-range value reads or overwrites neighboring memory.
- A common idiom is to loop with a counter to inspect each character in turn:

```cobol
PERFORM VARYING WS-I FROM 1 BY 1 UNTIL WS-I > 12
    IF WS-TEXT(WS-I:1) = SPACE
        ...
    END-IF
END-PERFORM
```

---

## 13.2 `STRING` and `UNSTRING`

### `STRING`: join pieces together

`STRING` copies several sources, one after another, into a single receiving field:

```text
STRING source-1 [source-2 ...] DELIMITED BY delimiter
       [source-3 ... DELIMITED BY delimiter]
       INTO receiver
       [WITH POINTER position]
       [ON OVERFLOW statements]
       [NOT ON OVERFLOW statements]
END-STRING
```

Each group of sources must say where to stop copying, using `DELIMITED BY`:

| `DELIMITED BY` | Meaning |
|---|---|
| `SIZE` | Copy the entire source |
| `"literal"` or `SPACE` | Copy up to (not including) the first occurrence of that value |

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. STRING1.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-FIRST    PIC X(10) VALUE "Maria".
01 WS-LAST     PIC X(10) VALUE "Santos".
01 WS-FULL     PIC X(25) VALUE SPACES.
01 WS-PTR      PIC 99.
PROCEDURE DIVISION.
    STRING WS-FIRST DELIMITED BY SPACE
           " "      DELIMITED BY SIZE
           WS-LAST  DELIMITED BY SPACE
           INTO WS-FULL
    END-STRING
    DISPLAY "[" WS-FULL "]"

    *> Appending with a pointer
    MOVE SPACES TO WS-FULL
    MOVE 1 TO WS-PTR
    STRING "A" DELIMITED BY SIZE INTO WS-FULL WITH POINTER WS-PTR
    STRING "B" DELIMITED BY SIZE INTO WS-FULL WITH POINTER WS-PTR
    STRING "C" DELIMITED BY SIZE INTO WS-FULL WITH POINTER WS-PTR
    DISPLAY "[" WS-FULL "] next position is " WS-PTR
    STOP RUN.
```

```text
[Maria Santos             ]
[ABC                      ] next position is 04
```

Important behaviors:

- **`STRING` does not clear the receiver.** It overwrites only the characters it writes. Characters beyond them keep their old contents, so `MOVE SPACES TO receiver` first (unless you intend to append).
- The `POINTER` item tells `STRING` where in the receiver to start writing (default 1), and afterwards it holds the next free position. You must initialize it to 1 yourself.
- `ON OVERFLOW` runs if the receiver fills up before all sources were copied, or if the pointer is out of range.
- `DELIMITED BY SPACE` stops at the **first** space, so `"New York"` would be cut to `"New"`. For text that may contain spaces, trim it first with `FUNCTION TRIM` (see 13.4).

### `UNSTRING`: split text apart

`UNSTRING` splits one source into several receivers at delimiters:

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. UNSTR1.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-LINE      PIC X(30) VALUE "Santos,Maria,1985,Manila".
01 WS-LAST      PIC X(10).
01 WS-FIRST     PIC X(10).
01 WS-YEAR      PIC X(4).
01 WS-CITY      PIC X(10).
01 WS-FIELDS    PIC 9 VALUE 0.
01 WS-LEN       PIC 99.
PROCEDURE DIVISION.
    UNSTRING WS-LINE DELIMITED BY ","
        INTO WS-LAST
             WS-FIRST
             WS-YEAR
             WS-CITY COUNT IN WS-LEN
        TALLYING IN WS-FIELDS
    END-UNSTRING
    DISPLAY "Last:   [" WS-LAST "]"
    DISPLAY "First:  [" WS-FIRST "]"
    DISPLAY "Year:   [" WS-YEAR "]"
    DISPLAY "City:   [" WS-CITY "] length " WS-LEN
    DISPLAY "Fields filled: " WS-FIELDS
    STOP RUN.
```

```text
Last:   [Santos    ]
First:  [Maria     ]
Year:   [1985]
City:   [Manila    ] length 12
Fields filled: 4
```

Notice that `WS-LEN` reports 12, not 6. The last field takes the remainder of the 30-character source (`Manila` plus its six trailing padding spaces), because no further comma ends it. Use `FUNCTION TRIM` if you need the true text length.

Clauses you can add:

| Clause | Purpose |
|---|---|
| `DELIMITED BY "x" OR "y"` | Several single delimiters |
| `DELIMITED BY ALL ","` | Treat a run of consecutive delimiters as one |
| `INTO a COUNT IN n` | Store the number of characters moved into `a` |
| `INTO a DELIMITER IN d` | Store the delimiter that ended the field |
| `WITH POINTER p` | Start scanning at position `p`; it is updated afterwards |
| `TALLYING IN t` | Count the receiving fields that were filled |
| `ON OVERFLOW` | Runs if there were more fields than receivers, or the pointer was invalid |

Unlike `STRING`, `UNSTRING` **does** pad each receiver with spaces (alphanumeric) or zeros (numeric, aligned on the decimal point), so you do not need to clear them beforehand.

---

## 13.3 `INSPECT` (Tallying and Replacing)

`INSPECT` scans a field character by character to **count**, **replace**, or **translate** characters, in place.

### `INSPECT ... TALLYING`: count

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. INSPECT1.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-TEXT      PIC X(20) VALUE "BANANA BREAD RECIPE".
01 WS-A-COUNT   PIC 99 VALUE 0.
01 WS-LEAD      PIC 99 VALUE 0.
01 WS-ALL-CHARS PIC 99 VALUE 0.
01 WS-ZEROES    PIC X(8) VALUE "00001200".
PROCEDURE DIVISION.
    INSPECT WS-TEXT TALLYING WS-A-COUNT FOR ALL "A"
    DISPLAY "Letter A appears " WS-A-COUNT " times"

    INSPECT WS-ZEROES TALLYING WS-LEAD FOR LEADING "0"
    DISPLAY "Leading zeros: " WS-LEAD

    INSPECT WS-TEXT TALLYING WS-ALL-CHARS FOR CHARACTERS
        BEFORE INITIAL " "
    DISPLAY "Characters before first space: " WS-ALL-CHARS
    STOP RUN.
```

```text
Letter A appears 04 times
Leading zeros: 04
Characters before first space: 06
```

Note that `TALLYING` **adds** to the counter, so set it to zero first.

The counting phrases are:

| Phrase | Counts |
|---|---|
| `FOR ALL "x"` | Every occurrence of `x` |
| `FOR LEADING "x"` | Occurrences of `x` at the start only |
| `FOR CHARACTERS` | Every character |

### `INSPECT ... REPLACING`

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. INSPECT2.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-PHONE     PIC X(12) VALUE "632-555-1234".
01 WS-NUM       PIC X(8)  VALUE "00001200".
01 WS-WORDS     PIC X(15) VALUE "AAA BBB AAA CCC".
PROCEDURE DIVISION.
    INSPECT WS-PHONE REPLACING ALL "-" BY "."
    DISPLAY WS-PHONE

    INSPECT WS-NUM REPLACING LEADING "0" BY SPACE
    DISPLAY "[" WS-NUM "]"

    INSPECT WS-WORDS REPLACING FIRST "AAA" BY "XXX"
    DISPLAY WS-WORDS
    STOP RUN.
```

```text
632.555.1234
[    1200]
XXX BBB AAA CCC
```

| Phrase | Replaces |
|---|---|
| `ALL "x" BY "y"` | Every occurrence |
| `LEADING "x" BY "y"` | Only the run of `x` at the start |
| `FIRST "x" BY "y"` | Only the first occurrence |
| `CHARACTERS BY "y"` | Every character |

The search and replacement strings must be **the same length** (the replacement happens in place).

### Limiting the scan: `BEFORE` and `AFTER`

Add `BEFORE INITIAL "x"` or `AFTER INITIAL "x"` to restrict the range to the part of the field before or after the first `x`:

```cobol
INSPECT WS-EMAIL REPLACING ALL "a" BY "4" BEFORE INITIAL "@"
```

### `INSPECT ... CONVERTING`

`CONVERTING` translates characters one-for-one. The classic use is changing case:

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. INSPECT3.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-NAME      PIC X(10) VALUE "maria luz".
PROCEDURE DIVISION.
    INSPECT WS-NAME CONVERTING
        "abcdefghijklmnopqrstuvwxyz" TO "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
    DISPLAY WS-NAME
    STOP RUN.
```

```text
MARIA LUZ 
```

(The newer `FUNCTION UPPER-CASE` does the same thing in one call, as shown next.)

---

## 13.4 Intrinsic String Functions

Intrinsic functions return a value and can be used as a source in `MOVE`, `DISPLAY`, `STRING`, and comparisons.

| Function | Result |
|---|---|
| `LENGTH(x)` | Number of characters in `x` (the **declared** size for a field) |
| `TRIM(x)` | `x` without leading and trailing spaces. `TRIM(x LEADING)` or `TRIM(x TRAILING)` trims one side only |
| `UPPER-CASE(x)`, `LOWER-CASE(x)` | Case conversion |
| `REVERSE(x)` | Characters in reverse order |
| `CONCATENATE(a b c ...)` | The arguments joined end to end (a GnuCOBOL extension in some versions; check your compiler) |
| `SUBSTITUTE(x a1 b1 ...)` | Replace each `a` with `b` (GnuCOBOL extension; `a` and `b` can have different lengths) |
| `ORD(c)` | Position of a character in the collating sequence (1-based) |
| `CHAR(n)` | The character at position `n` |
| `NUMVAL(x)` | Converts text such as `"  -123.45 "` to a number |
| `NUMVAL-C(x)` | Like `NUMVAL`, but also accepts a currency symbol and commas (`"$1,234.50"`) |
| `TEST-NUMVAL(x)` | 0 if `x` is valid for `NUMVAL`, otherwise the position of the first bad character |

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. STRFUNCS.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-NAME      PIC X(20) VALUE "   Maria Santos".
01 WS-AMOUNT-TX PIC X(12) VALUE "$1,234.50".
01 WS-AMOUNT    PIC 9(6)V99.
01 WS-OUT       PIC X(30).
PROCEDURE DIVISION.
    DISPLAY "Declared length:  " FUNCTION LENGTH(WS-NAME)
    DISPLAY "Trimmed length:   "
            FUNCTION LENGTH(FUNCTION TRIM(WS-NAME))
    DISPLAY "Trimmed:          [" FUNCTION TRIM(WS-NAME) "]"
    DISPLAY "Upper:            " FUNCTION UPPER-CASE(WS-NAME)
    DISPLAY "Reversed:         " FUNCTION REVERSE(FUNCTION TRIM(WS-NAME))

    COMPUTE WS-AMOUNT = FUNCTION NUMVAL-C(WS-AMOUNT-TX)
    DISPLAY "Amount as number: " WS-AMOUNT

    STRING "Hello, " DELIMITED BY SIZE
           FUNCTION TRIM(WS-NAME) DELIMITED BY SIZE
           "!" DELIMITED BY SIZE
           INTO WS-OUT
    END-STRING
    DISPLAY WS-OUT
    STOP RUN.
```

```text
Declared length:  20
Trimmed length:   000000012
Trimmed:          [Maria Santos]
Upper:               MARIA SANTOS     
Reversed:         sotnaS airaM
Amount as number: 001234.50
Hello, Maria Santos!
```

### Practical notes

- `DISPLAY` prints function results such as `LENGTH` in their raw numeric form (here `000000012`). Move the result to a `PIC 99` item or an edited item if you want it formatted.
- A field's `LENGTH` is its **declared** size, never the length of the text inside it. To measure the text, trim first: `FUNCTION LENGTH(FUNCTION TRIM(x))`.
- `TRIM` of an all-space field returns a field of length 1 in some compilers, and a zero-length value in others. Test for `x = SPACES` first when that matters.
- Prefer `NUMVAL` / `NUMVAL-C` over moving text into a numeric field. They handle signs, spaces, decimal points, and currency symbols, and `TEST-NUMVAL` lets you validate first.
- Mainframe compilers support many of these functions (`LENGTH`, `TRIM`, `UPPER-CASE`, `LOWER-CASE`, `REVERSE`, `NUMVAL`, `NUMVAL-C`, `ORD`, `CHAR`), but not all GnuCOBOL extensions. When portability to IBM matters, test with `-std=ibm` or consult the compiler manual.

---

[Previous]([12]-Paragraphs-and-Sections.md) | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([14]-Sequential-Files.md)