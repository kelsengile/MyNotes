[Previous]([5]-Variables-and-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([7]-Display-and-Accept.md)

*Core Syntax*

# Lesson 6 - Group Items, Records & Tables (`OCCURS`)

Real business data is rarely a single value. A customer has a name, address, and balance; an invoice has many line items. COBOL describes this structure directly in the `DATA DIVISION` using group items, `OCCURS` tables, and `REDEFINES`.

---

## 6.1 Group vs Elementary Items

An **elementary item** has a `PIC` clause and holds one value. A **group item** has no `PIC`; it is a name for the collection of items beneath it (those with higher level numbers).

```cobol
01 WS-DATE.                      *> group item
   05 WS-YEAR     PIC 9(4).      *> elementary
   05 WS-MONTH    PIC 99.        *> elementary
   05 WS-DAY      PIC 99.        *> elementary
```

Key facts:

- A group is always treated as **alphanumeric** (text) when you use it as a whole, even if all its children are numeric.
- The group's size is the sum of its children's sizes. `WS-DATE` is 8 bytes.
- You can refer to the whole group (`WS-DATE`) or to any piece (`WS-MONTH`).
- A group ends when the next item has the same or a lower level number.

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. GROUPS.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-DATE.
   05 WS-YEAR     PIC 9(4) VALUE 2026.
   05 WS-MONTH    PIC 99   VALUE 3.
   05 WS-DAY      PIC 99   VALUE 9.
PROCEDURE DIVISION.
    DISPLAY "Whole group: " WS-DATE
    DISPLAY "Month only:  " WS-MONTH
    MOVE 12 TO WS-MONTH
    DISPLAY "After MOVE:  " WS-DATE
    STOP RUN.
```

```text
Whole group: 20260309
Month only:  03
After MOVE:  20261209
```

---

## 6.2 Records and Nested Structures

A **record** is simply a level-01 group describing one unit of data, such as one line of a file or one customer. Groups can nest as deeply as needed (levels 01 to 49).

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. RECORDS.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 CUSTOMER-RECORD.
   05 CUST-ID          PIC 9(6).
   05 CUST-NAME.
      10 CUST-FIRST    PIC X(10).
      10 CUST-LAST     PIC X(12).
   05 CUST-ADDRESS.
      10 CUST-STREET   PIC X(20).
      10 CUST-CITY     PIC X(12).
      10 CUST-ZIP      PIC X(5).
   05 CUST-BALANCE     PIC S9(7)V99 VALUE ZERO.
PROCEDURE DIVISION.
    MOVE 100245        TO CUST-ID
    MOVE "Ana"         TO CUST-FIRST
    MOVE "Reyes"       TO CUST-LAST
    MOVE "12 Rizal St" TO CUST-STREET
    MOVE "Imus"        TO CUST-CITY
    MOVE "4103"        TO CUST-ZIP
    MOVE 1520.5        TO CUST-BALANCE
    DISPLAY "Name:    " CUST-NAME
    DISPLAY "Address: " CUST-ADDRESS
    DISPLAY "Record length: " FUNCTION LENGTH(CUSTOMER-RECORD)
    DISPLAY "Whole record: [" CUSTOMER-RECORD "]"
    STOP RUN.
```

```text
Name:    Ana       Reyes       
Address: 12 Rizal St         Imus        4103 
Record length: 74
Whole record: [100245Ana       Reyes       12 Rizal St         Imus        4103 000152050]
```

The record is 74 bytes: 6 + 10 + 12 + 20 + 12 + 5 + 9. Notice that the balance appears as nine raw digits (`000152050`) with no decimal point and no separate sign character. A signed `DISPLAY` number keeps its sign inside the last digit, so the sign never takes extra space.

**Why records matter.** Files are read and written one record at a time, so a record layout is the contract between programs. Layouts shared by many programs live in **copybooks** (Lesson 20).

**Initializing a record.** `INITIALIZE CUSTOMER-RECORD` sets every numeric child to zero and every alphanumeric child to spaces.

**Duplicate names and qualification.** If two records both contain `ID`, say which one with `OF`: `MOVE 5 TO ID OF ORDER-RECORD`.

---

## 6.3 `OCCURS` and Single-Dimension Tables

`OCCURS` repeats an item a fixed number of times. COBOL calls arrays **tables**.

```cobol
01 WS-SCORES.
   05 WS-SCORE    PIC 9(3) OCCURS 5 TIMES.
```

This defines five 3-digit numbers in a row (15 bytes). You refer to one entry with a **subscript** in parentheses. **Subscripts start at 1**, not 0.

```cobol
MOVE 90 TO WS-SCORE(1)
MOVE 75 TO WS-SCORE(5)
```

### Filling and summing a table

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. TABLE1.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-SCORES.
   05 WS-SCORE    PIC 9(3) OCCURS 5 TIMES.
01 WS-I           PIC 9    VALUE 0.
01 WS-TOTAL       PIC 9(4) VALUE 0.
01 WS-AVERAGE     PIC 9(3)V9 VALUE 0.
PROCEDURE DIVISION.
    MOVE 90 TO WS-SCORE(1)
    MOVE 85 TO WS-SCORE(2)
    MOVE 70 TO WS-SCORE(3)
    MOVE 95 TO WS-SCORE(4)
    MOVE 60 TO WS-SCORE(5)
    PERFORM VARYING WS-I FROM 1 BY 1 UNTIL WS-I > 5
        ADD WS-SCORE(WS-I) TO WS-TOTAL
    END-PERFORM
    COMPUTE WS-AVERAGE = WS-TOTAL / 5
    DISPLAY "Total:   " WS-TOTAL
    DISPLAY "Average: " WS-AVERAGE
    STOP RUN.
```

```text
Total:   0400
Average: 080.0
```

### Tables of groups

An `OCCURS` on a group item gives you a table of records:

```cobol
01 WS-EMPLOYEE-TABLE.
   05 WS-EMPLOYEE OCCURS 3 TIMES.
      10 EMP-ID      PIC 9(3).
      10 EMP-NAME    PIC X(10).
      10 EMP-SALARY  PIC 9(5).
```

You write `EMP-NAME(2)` to reach the name in the second entry.

### Initial values for tables

`VALUE` on an `OCCURS` item sets every occurrence (in COBOL 85 and later):

```cobol
05 WS-COUNT   PIC 9(3) VALUE ZERO OCCURS 10 TIMES.
```

For a table of **different** constants, define the values in a group and use `REDEFINES` (Lesson 6.5).

### Variable-length tables

`OCCURS ... DEPENDING ON` lets the number of active entries vary at run time. The size you declare is the maximum:

```cobol
05 WS-ITEM  PIC X(10) OCCURS 1 TO 100 TIMES DEPENDING ON WS-ITEM-COUNT.
```

`WS-ITEM-COUNT` must be a numeric item you maintain. This mostly matters for variable-length file records.

### Subscripts vs indexes

You can subscript with any integer data item or literal. A **subscript** holds an occurrence number (1, 2, 3). An **index** (declared with `INDEXED BY`, below) holds a displacement internally, is usually faster, and is manipulated with `SET`. Prefer `COMP` subscripts or indexes for large tables.

> **Bounds are not checked by default.** Referencing `WS-SCORE(6)` in the table above silently reads whatever memory follows. Compile with `-debug` while developing (Lesson 2) so this stops with an error.

---

## 6.4 Multi-Dimensional Tables, `INDEXED BY`, and `SEARCH`

### Multi-dimensional tables

Nest `OCCURS` to build rows and columns. COBOL allows up to seven dimensions (GnuCOBOL allows more with extensions):

```cobol
01 WS-SALES-TABLE.
   05 WS-REGION OCCURS 3 TIMES.
      10 WS-MONTH-SALES  PIC 9(5) OCCURS 4 TIMES.
```

Here `WS-MONTH-SALES(2, 3)` is region 2, month 3. List the **outermost subscript first**.

### `INDEXED BY` and `SET`

An index is declared with the table and does not need its own `PIC`:

```cobol
05 WS-CODE-ENTRY OCCURS 4 TIMES INDEXED BY CODE-IDX.
```

Use `SET` to change an index (not `MOVE` or `ADD`):

```cobol
SET CODE-IDX TO 1
SET CODE-IDX UP BY 1
SET CODE-IDX DOWN BY 1
SET WS-NUM TO CODE-IDX          *> convert index to an ordinary number
```

### Linear lookup: `SEARCH`

`SEARCH` examines entries one at a time, starting from the current index value, and stops at the first match:

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. LOOKUP.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-CODE-TABLE.
   05 FILLER PIC X(13) VALUE "ENGEngineers ".
   05 FILLER PIC X(13) VALUE "ACCAccounting".
   05 FILLER PIC X(13) VALUE "HR HumanRes  ".
   05 FILLER PIC X(13) VALUE "ITSInfo Tech ".
01 WS-DEPT-TABLE REDEFINES WS-CODE-TABLE.
   05 WS-DEPT OCCURS 4 TIMES INDEXED BY DEPT-IDX.
      10 DEPT-CODE   PIC X(3).
      10 DEPT-NAME   PIC X(10).
01 WS-SEARCH-CODE    PIC X(3) VALUE "HR ".
PROCEDURE DIVISION.
    SET DEPT-IDX TO 1
    SEARCH WS-DEPT
        AT END
            DISPLAY "Code not found: " WS-SEARCH-CODE
        WHEN DEPT-CODE(DEPT-IDX) = WS-SEARCH-CODE
            DISPLAY "Found: " DEPT-NAME(DEPT-IDX)
    END-SEARCH
    STOP RUN.
```

```text
Found: HumanRes  
```

Always `SET` the index to 1 first. `SEARCH` does not reset it. `AT END` runs when no entry matched.

> The sample entries above are 13 characters each (3-character code plus 10-character name), which is why the `FILLER` pictures are `X(13)`. Mismatched lengths are a common source of silent bugs, so always count.

### Binary lookup: `SEARCH ALL`

`SEARCH ALL` performs a binary search, so it is far faster on large tables. It requires the table to be **sorted** on the key, declared with `ASCENDING KEY` (or `DESCENDING KEY`) and `INDEXED BY`. It uses only equality tests and ignores the starting index.

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. BINLOOK.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-RATE-DATA.
   05 FILLER PIC X(8) VALUE "A0100010".
   05 FILLER PIC X(8) VALUE "B0200020".
   05 FILLER PIC X(8) VALUE "C0300030".
   05 FILLER PIC X(8) VALUE "D0400040".
01 WS-RATE-TABLE REDEFINES WS-RATE-DATA.
   05 WS-RATE OCCURS 4 TIMES
         ASCENDING KEY IS RATE-CLASS
         INDEXED BY RATE-IDX.
      10 RATE-CLASS   PIC X.
      10 RATE-BASE    PIC 9(3)V99.
      10 RATE-EXTRA   PIC 9(2).
01 WS-WANTED         PIC X VALUE "C".
PROCEDURE DIVISION.
    SEARCH ALL WS-RATE
        AT END
            DISPLAY "No rate class " WS-WANTED
        WHEN RATE-CLASS(RATE-IDX) = WS-WANTED
            DISPLAY "Class " RATE-CLASS(RATE-IDX)
                    " base " RATE-BASE(RATE-IDX)
                    " extra " RATE-EXTRA(RATE-IDX)
    END-SEARCH
    STOP RUN.
```

```text
Class C base 030.00 extra 30
```

If the table is not actually in key order, `SEARCH ALL` returns wrong answers without any error.

### A two-dimensional example

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. GRID.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-SALES-TABLE.
   05 WS-REGION OCCURS 3 TIMES.
      10 WS-MONTH-SALES  PIC 9(4) OCCURS 4 TIMES.
01 R   PIC 9.
01 M   PIC 9.
01 WS-REGION-TOTAL PIC 9(5).
PROCEDURE DIVISION.
    PERFORM VARYING R FROM 1 BY 1 UNTIL R > 3
        PERFORM VARYING M FROM 1 BY 1 UNTIL M > 4
            COMPUTE WS-MONTH-SALES(R, M) = R * 100 + M * 10
        END-PERFORM
    END-PERFORM
    PERFORM VARYING R FROM 1 BY 1 UNTIL R > 3
        MOVE 0 TO WS-REGION-TOTAL
        PERFORM VARYING M FROM 1 BY 1 UNTIL M > 4
            ADD WS-MONTH-SALES(R, M) TO WS-REGION-TOTAL
        END-PERFORM
        DISPLAY "Region " R " total: " WS-REGION-TOTAL
    END-PERFORM
    STOP RUN.
```

```text
Region 1 total: 00500
Region 2 total: 00900
Region 3 total: 01300
```

---

## 6.5 `REDEFINES` and `RENAMES`

### `REDEFINES`: two views of the same memory

`REDEFINES` lets a second description share the storage of an earlier item at the same level. Writing to one view changes the other, because it is the same bytes.

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. REDEF.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-DATE-TEXT      PIC X(8) VALUE "20261231".
01 WS-DATE-PARTS REDEFINES WS-DATE-TEXT.
   05 WS-YYYY        PIC 9(4).
   05 WS-MM          PIC 99.
   05 WS-DD          PIC 99.
PROCEDURE DIVISION.
    DISPLAY "Year:  " WS-YYYY
    DISPLAY "Month: " WS-MM
    DISPLAY "Day:   " WS-DD
    STOP RUN.
```

```text
Year:  2026
Month: 12
Day:   31
```

Rules:

- `REDEFINES` must immediately follow the item it redefines, with the **same level number**.
- The redefining item should not be larger than the original (except at level 01 in some compilers).
- Do not put `VALUE` on the redefining item (except level 88).
- Typical uses: viewing one field in two ways (text vs. parts), building constant tables as shown in 6.4, and **variant records** where the meaning of the bytes depends on a type code.

A variant record:

```cobol
01 WS-TRANSACTION.
   05 TRAN-TYPE        PIC X.
   05 TRAN-BODY        PIC X(20).
   05 TRAN-DEPOSIT REDEFINES TRAN-BODY.
      10 DEP-ACCOUNT   PIC 9(8).
      10 DEP-AMOUNT    PIC 9(5)V99.
      10 FILLER        PIC X(5).
   05 TRAN-NOTE REDEFINES TRAN-BODY.
      10 NOTE-TEXT     PIC X(20).
```

The program checks `TRAN-TYPE` and then uses the matching view. Using the wrong view is not detected by the compiler, so it is easy to misuse.

### `FILLER`

`FILLER` is a reserved word for a data item you never refer to by name, such as padding between fields or one row of a constant table. Several `FILLER` items can appear in one record. In newer COBOL you may omit the word and write just the level and `PIC`.

### `RENAMES` (level 66)

A level-66 entry gives an alternate name to a *range* of consecutive items in the same record. It is rarely used, but you may meet it in legacy code:

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. RENAME.
DATA DIVISION.
WORKING-STORAGE SECTION.
01 WS-PERSON.
   05 P-FIRST      PIC X(6)  VALUE "MARIA ".
   05 P-MIDDLE     PIC X(6)  VALUE "LUZ   ".
   05 P-LAST       PIC X(6)  VALUE "SANTOS".
   66 P-FULL-NAME  RENAMES P-FIRST THRU P-LAST.
PROCEDURE DIVISION.
    DISPLAY P-FULL-NAME
    STOP RUN.
```

```text
MARIA LUZ   SANTOS
```

Level 66 entries must come **last** in the record, immediately after its final item, and cannot rename a level 01, 66, 77, or 88 item.

---

[Previous]([5]-Variables-and-Data-Types.md) | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([7]-Display-and-Accept.md)