[Previous]([6]-Group-Items-and-Tables.md) | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([8]-MOVE-and-Editing.md)

*Core Syntax*

# Lesson 7 - Input & Output with `DISPLAY` and `ACCEPT`

`DISPLAY` writes to the terminal and `ACCEPT` reads from it. They are the simplest I/O statements in COBOL, and they are what you will use for quick tests and small interactive programs. Real batch programs read and write files instead (Lesson 14).

---

## 7.1 `DISPLAY`

`DISPLAY` writes one or more operands, followed by a newline:

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. SHOW.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-NAME     PIC X(8)  VALUE "Carlos".
01 WS-COUNT    PIC 9(3)  VALUE 7.
PROCEDURE DIVISION.
    DISPLAY "Hello, World!"
    DISPLAY "Name: " WS-NAME
    DISPLAY "Count: " WS-COUNT " items"
    DISPLAY "Name between brackets: [" WS-NAME "]"
    DISPLAY "Line 1" " " "continues"
    STOP RUN.
```

Output:

```text
Hello, World!
Name: Carlos  
Count: 007 items
Name between brackets: [Carlos  ]
Line 1 continues
```

### What can you display?

- **Literals**: `"text"` or numbers.
- **Data items**: elementary or group items. A `PIC X(8)` item always prints all 8 characters, including trailing spaces. Use `FUNCTION TRIM(WS-NAME)` to remove them.
- **Figurative constants**: `SPACE`, `ZERO`, `QUOTE`, and so on.
- **Intrinsic functions**: `DISPLAY FUNCTION CURRENT-DATE`.

Operands are joined with **no separator**, so add spaces inside your literals as needed.

### Formatting numbers

`DISPLAY` shows numeric items in their stored form (leading zeros, and in some compilers an implied decimal point or a sign inside the last digit). For readable output, move the value to an edited picture first (Lesson 8):

```cobol
01 WS-AMOUNT         PIC 9(5)V99   VALUE 1234.5.
01 WS-AMOUNT-EDIT    PIC ZZ,ZZ9.99.
...
MOVE WS-AMOUNT TO WS-AMOUNT-EDIT
DISPLAY "Amount: " WS-AMOUNT-EDIT
```

### Options

| Form | Effect |
|---|---|
| `DISPLAY "Enter name: " WITH NO ADVANCING` | Stays on the same line (no newline), useful for prompts |
| `DISPLAY "Error!" UPON SYSERR` | Writes to the standard error stream (`CONSOLE` is the default target) |
| `DISPLAY X"0A"` | Outputs a hexadecimal character (here, a line feed) |

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. PROMPT.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-DOTS     PIC X(3) VALUE "...".
PROCEDURE DIVISION.
    DISPLAY "Loading" WITH NO ADVANCING
    DISPLAY WS-DOTS WITH NO ADVANCING
    DISPLAY " done"
    DISPLAY "This goes to the error stream" UPON SYSERR
    STOP RUN.
```

```text
Loading... done
```

(The last line goes to standard error, so it appears in your terminal but not if you redirect output with `>`.)

> **Mainframe note.** On z/OS, `DISPLAY` writes to `SYSOUT`, the job's output listing, so it is often used for logging in batch jobs. `DISPLAY ... UPON CONSOLE` writes to the operator console, which is not something you should do casually.

---

## 7.2 `ACCEPT` from the User, Date, and Time

`ACCEPT` stores data from an outside source into a data item.

### From the user (standard input)

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. GREET.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-NAME     PIC X(20).
01 WS-AGE      PIC 99.
PROCEDURE DIVISION.
    DISPLAY "What is your name? " WITH NO ADVANCING
    ACCEPT WS-NAME
    DISPLAY "How old are you? " WITH NO ADVANCING
    ACCEPT WS-AGE
    DISPLAY "Hello, " FUNCTION TRIM(WS-NAME) "! Next year you will be "
            WS-AGE " + 1."
    STOP RUN.
```

Sample session (what you type is shown after the prompts):

```text
What is your name? Lena
How old are you? 29
Hello, Lena! Next year you will be 29 + 1.
```

How `ACCEPT` stores what you type:

- Into `PIC X`: the text is left-justified and padded with spaces (or truncated if too long).
- Into `PIC 9`: the digits typed are aligned on the decimal point and stored. Type exactly what the picture expects. Non-digit input gives unpredictable results, so check it with `IF WS-AGE IS NUMERIC` (Lesson 10) or read into a `PIC X` item and convert with `FUNCTION NUMVAL` (Lesson 13).
- There is no built-in input validation. COBOL trusts the programmer.

### From the system: date and time

`ACCEPT ... FROM` reads values supplied by the operating system:

| Statement | Result | Format |
|---|---|---|
| `ACCEPT d FROM DATE` | Two-digit year | `YYMMDD` (6 digits) |
| `ACCEPT d FROM DATE YYYYMMDD` | Four-digit year | `YYYYMMDD` (8 digits) |
| `ACCEPT d FROM DAY` | Julian date, 2-digit year | `YYDDD` |
| `ACCEPT d FROM DAY YYYYDDD` | Julian date, 4-digit year | `YYYYDDD` |
| `ACCEPT d FROM DAY-OF-WEEK` | One digit | 1 = Monday ... 7 = Sunday |
| `ACCEPT t FROM TIME` | Time of day | `HHMMSSss` (hundredths of a second) |

Always prefer the four-digit-year forms (see the Y2K lessons in Lesson 23).

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. TODAY.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-DATE.
   05 WS-YEAR     PIC 9(4).
   05 WS-MONTH    PIC 99.
   05 WS-DAY      PIC 99.
01 WS-TIME.
   05 WS-HOUR     PIC 99.
   05 WS-MIN      PIC 99.
   05 WS-SEC      PIC 99.
   05 WS-HUND     PIC 99.
01 WS-DOW         PIC 9.
PROCEDURE DIVISION.
    ACCEPT WS-DATE FROM DATE YYYYMMDD
    ACCEPT WS-TIME FROM TIME
    ACCEPT WS-DOW  FROM DAY-OF-WEEK
    DISPLAY "Date: " WS-YEAR "-" WS-MONTH "-" WS-DAY
    DISPLAY "Time: " WS-HOUR ":" WS-MIN ":" WS-SEC
    DISPLAY "Day of week (1=Mon): " WS-DOW
    STOP RUN.
```

Example output (the values depend on when you run it):

```text
Date: 2026-10-17
Time: 14:05:32
Day of week (1=Mon): 6
```

Splitting the date into year, month, and day works because the receiving item is a **group** whose children line up with the digits (see Lesson 6).

### From the command line and environment

GnuCOBOL (and many other compilers) can also read program arguments and environment variables:

```cobol
ACCEPT WS-ARGS FROM COMMAND-LINE          *> everything typed after the program name
ACCEPT WS-HOME FROM ENVIRONMENT "HOME"    *> value of an environment variable
```

These are extensions to the classic standard, but they are very handy for passing a file name into a program. (IBM mainframe programs get their parameters from the `PARM=` on the JCL `EXEC` statement through the `LINKAGE SECTION`, which Lesson 19 covers.)

### Reading until the end

When input comes from a file redirected to standard input, `ACCEPT` has no `AT END` clause. For repeated reading of data, use a real file with `READ ... AT END` (Lesson 14). `ACCEPT` is for small, interactive amounts of data.

---

## 7.3 Screen Section Basics

For text-mode forms with positioned fields, colors, and highlights, GnuCOBOL provides the `SCREEN SECTION` (part of the 2002 standard, and similar to Micro Focus screens). It is used for interactive programs, not for batch processing. It works in a terminal window and needs a curses library, which the standard GnuCOBOL install includes.

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. LOGIN.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-USER      PIC X(15).
01 WS-PASS      PIC X(15).
SCREEN SECTION.
01 LOGIN-SCREEN.
   05 BLANK SCREEN.
   05 LINE 2  COLUMN 10 VALUE "=== System Login ===".
   05 LINE 4  COLUMN 5  VALUE "User name: ".
   05 LINE 4  COLUMN 17 PIC X(15) USING WS-USER.
   05 LINE 5  COLUMN 5  VALUE "Password: ".
   05 LINE 5  COLUMN 17 PIC X(15) USING WS-PASS SECURE.
PROCEDURE DIVISION.
    DISPLAY LOGIN-SCREEN
    ACCEPT LOGIN-SCREEN
    DISPLAY "Welcome, " FUNCTION TRIM(WS-USER) AT LINE 8 COLUMN 5
    STOP RUN.
```

How it works:

- A screen description is a hierarchy like a record, but each entry says **where** (`LINE`, `COLUMN`) and **what** (`VALUE` for fixed text, `PIC` plus a data item for fields).
- `DISPLAY screen-name` draws the whole screen.
- `ACCEPT screen-name` lets the user fill in all the input fields and press Enter. Fields with `USING` both show the item's current contents and store the edited text back.
- Clauses you will use most:

| Clause | Effect |
|---|---|
| `LINE n`, `COLUMN n` | Position (absolute) |
| `BLANK SCREEN` / `BLANK LINE` | Clear the screen or a line |
| `FROM item` | Show an item's value (output only) |
| `TO item` | Accept into an item (input only) |
| `USING item` | Show the value and accept changes (input and output) |
| `SECURE` | Do not echo typed characters (passwords) |
| `FOREGROUND-COLOR n`, `BACKGROUND-COLOR n` | Colors 0 to 7 |
| `HIGHLIGHT`, `REVERSE-VIDEO`, `UNDERLINE`, `BELL` | Display attributes |
| `REQUIRED`, `AUTO`, `FULL` | Entry rules for input fields |

You can also position a single `DISPLAY` or `ACCEPT` without a screen section, using `AT LINE n COLUMN n` (as in the final `DISPLAY` above).

**Portability.** Screen handling is the least portable part of COBOL. IBM mainframe programs do not use it; they use **BMS maps** under CICS instead (Lesson 32). Treat the `SCREEN SECTION` as a convenience for terminal tools and teaching, not a skill that transfers to the mainframe.

---

[Previous]([6]-Group-Items-and-Tables.md) | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([8]-MOVE-and-Editing.md)