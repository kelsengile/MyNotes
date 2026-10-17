[Previous]([13]-String-Handling.md) | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([15]-Indexed-and-Relative-Files.md)

*Files & Data Processing*

# Lesson 14 - Sequential File Processing

Business COBOL programs spend most of their time reading and writing files. This lesson covers **sequential files**, where records are stored one after another and read in order. It is the foundation for every batch program.

---

## 14.1 `SELECT`, `ASSIGN`, and `FD`

Using a file takes three declarations that work together:

| Where | Statement | Role |
|---|---|---|
| `ENVIRONMENT DIVISION` | `SELECT ... ASSIGN TO ...` | Connects a **logical** file name to a **physical** file |
| `DATA DIVISION`, `FILE SECTION` | `FD file-name` plus record layout | Describes the records in the file |
| `PROCEDURE DIVISION` | `OPEN`, `READ`, `WRITE`, `CLOSE` | Does the I/O |

```cobol
ENVIRONMENT DIVISION.
INPUT-OUTPUT SECTION.
FILE-CONTROL.
    SELECT EMPLOYEE-FILE
        ASSIGN TO "employees.dat"
        ORGANIZATION IS LINE SEQUENTIAL
        FILE STATUS IS WS-EMP-STATUS.

DATA DIVISION.
FILE SECTION.
FD  EMPLOYEE-FILE.
01  EMPLOYEE-RECORD.
    05  EMP-ID        PIC 9(4).
    05  EMP-NAME      PIC X(15).
    05  EMP-SALARY    PIC 9(5)V99.

WORKING-STORAGE SECTION.
01  WS-EMP-STATUS     PIC XX.
```

### The `SELECT` clauses

| Clause | Meaning |
|---|---|
| `ASSIGN TO "name"` | The physical file. In GnuCOBOL, a quoted literal is a file name; a data name holds the file name at run time |
| `ORGANIZATION IS ...` | `SEQUENTIAL` (binary fixed-length records), `LINE SEQUENTIAL` (text file, one record per line), `INDEXED`, or `RELATIVE` |
| `ACCESS MODE IS SEQUENTIAL` | Default for sequential files |
| `FILE STATUS IS data-name` | A two-character item that receives a status code after every I/O statement (see 14.3) |

`LINE SEQUENTIAL` is what you want for ordinary text files on a PC: each record ends with a newline, and trailing spaces are removed when writing. Plain `SEQUENTIAL` is the mainframe model, where records are fixed-length blocks of bytes with no line endings.

### The `FD` entry

- The `FD` names the file and is followed by one or more `01` record descriptions. The record area is the **buffer** for the file: after a `READ`, the new record is in it; before a `WRITE`, you build the record in it.
- Multiple `01` records under one `FD` share the same storage (they implicitly redefine each other), which is used for files holding different record types.
- `VALUE` clauses are not used in the file section.
- Keep the layout in a **copybook** so every program uses the same one (Lesson 20).

> **Mainframe note.** On z/OS you write `ASSIGN TO EMPFILE`. `EMPFILE` is a DD name, and the JCL attaches a dataset with `//EMPFILE DD DSN=...`. The `FD` usually adds `RECORDING MODE IS F`, `BLOCK CONTAINS 0 RECORDS`, and `LABEL RECORDS ARE STANDARD`, so that the record format and blocking come from the dataset (Lessons 30 and 31).

---

## 14.2 `OPEN`, `READ`, `WRITE`, `CLOSE`

### `OPEN`

Before any I/O, open the file in a **mode**:

| Statement | Effect |
|---|---|
| `OPEN INPUT file` | Read only. The file must exist |
| `OPEN OUTPUT file` | Write only. Creates the file, or **erases** an existing one |
| `OPEN EXTEND file` | Write only, adding new records at the end of the existing ones |
| `OPEN I-O file` | Read and update records (`REWRITE`); mostly for indexed and relative files |

You can open several files in one statement: `OPEN INPUT IN-FILE OUTPUT OUT-FILE`.

### `WRITE`

`WRITE` takes the **record name** (not the file name) and sends the current contents of the record area to the file:

```cobol
MOVE 1001            TO EMP-ID
MOVE "ALVAREZ, JOSE" TO EMP-NAME
MOVE 2500.50         TO EMP-SALARY
WRITE EMPLOYEE-RECORD
```

`WRITE record FROM working-storage-item` first copies the item into the record. This is the common style because the record area is easily overwritten by the next `READ`.

### `READ`

`READ file` reads the next record into the record area. At end of file, the `AT END` branch runs:

```cobol
READ EMPLOYEE-FILE
    AT END
        SET END-OF-FILE TO TRUE
    NOT AT END
        PERFORM PROCESS-RECORD
END-READ
```

`READ file INTO ws-item` also copies the record into a working-storage item, which is useful when you need the data to survive the next read.

### `CLOSE`

`CLOSE file` finishes the I/O and releases the file. Always close what you open; unclosed output files may lose their last records. A closed file can be reopened.

### A complete write-then-read program

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. FILEDEMO.

ENVIRONMENT DIVISION.
INPUT-OUTPUT SECTION.
FILE-CONTROL.
    SELECT EMPLOYEE-FILE
        ASSIGN TO "employees.dat"
        ORGANIZATION IS LINE SEQUENTIAL
        FILE STATUS IS WS-STATUS.

DATA DIVISION.
FILE SECTION.
FD  EMPLOYEE-FILE.
01  EMPLOYEE-RECORD.
    05  EMP-ID        PIC 9(4).
    05  EMP-NAME      PIC X(15).
    05  EMP-SALARY    PIC 9(5)V99.

WORKING-STORAGE SECTION.
01  WS-STATUS         PIC XX.
01  WS-EOF            PIC X VALUE "N".
    88  END-OF-FILE         VALUE "Y".
01  WS-SALARY-EDIT    PIC ZZ,ZZ9.99.

PROCEDURE DIVISION.
0000-MAIN.
    PERFORM 1000-CREATE-FILE
    PERFORM 2000-LIST-FILE
    STOP RUN.

1000-CREATE-FILE.
    OPEN OUTPUT EMPLOYEE-FILE
    MOVE 1001 TO EMP-ID
    MOVE "ALVAREZ, JOSE" TO EMP-NAME
    MOVE 2500.50 TO EMP-SALARY
    WRITE EMPLOYEE-RECORD
    MOVE 1002 TO EMP-ID
    MOVE "BAUTISTA, ANA" TO EMP-NAME
    MOVE 3100.00 TO EMP-SALARY
    WRITE EMPLOYEE-RECORD
    MOVE 1003 TO EMP-ID
    MOVE "CRUZ, LITO" TO EMP-NAME
    MOVE 1875.25 TO EMP-SALARY
    WRITE EMPLOYEE-RECORD
    CLOSE EMPLOYEE-FILE.

2000-LIST-FILE.
    OPEN INPUT EMPLOYEE-FILE
    PERFORM UNTIL END-OF-FILE
        READ EMPLOYEE-FILE
            AT END
                SET END-OF-FILE TO TRUE
            NOT AT END
                MOVE EMP-SALARY TO WS-SALARY-EDIT
                DISPLAY EMP-ID " " EMP-NAME " " WS-SALARY-EDIT
        END-READ
    END-PERFORM
    CLOSE EMPLOYEE-FILE.
```

```text
1001 ALVAREZ, JOSE    2,500.50
1002 BAUTISTA, ANA    3,100.00
1003 CRUZ, LITO       1,875.25
```

After running it, a text file named `employees.dat` exists in the current folder. Its first line is `1001ALVAREZ, JOSE  0250050`: fields are stored back to back with no separators, and the implied decimal point is not stored. That is the **fixed-width record** style typical of COBOL data.

---

## 14.3 `AT END` and `FILE STATUS`

### `AT END`

`AT END` fires when `READ` finds no more records. It is the *normal* way for a read loop to finish, not an error. A `READ` that reaches the end without an `AT END` clause (and without a `FILE STATUS` item or declarative) will terminate the program with a runtime error, so always handle it.

### `FILE STATUS`

After **every** I/O statement, the `FILE STATUS` item contains a two-character code. Checking it is how you detect problems such as a missing file:

| Code | Meaning |
|---|---|
| `00` | Success |
| `02` | Success (duplicate alternate key, for indexed files) |
| `04` | Record length did not match the file description (success with a warning) |
| `10` | End of file reached |
| `21` | Key sequence error (indexed files) |
| `22` | Duplicate key (indexed files) |
| `23` | Record not found (indexed files) |
| `30` | Permanent error (the exact cause is system dependent) |
| `35` | `OPEN INPUT` or `I-O` on a file that does not exist |
| `37` | `OPEN` not permitted (permissions) |
| `39` | File attributes conflict with the program's description |
| `41` | `OPEN` on a file that is already open |
| `42` | `CLOSE` on a file that is not open |
| `43` | `DELETE` or `REWRITE` without a preceding successful `READ` |
| `46` | `READ` after end of file, or after a failed read |
| `47` / `48` / `49` | I/O verb used in the wrong open mode |

The first digit gives the class: `0` successful, `1` end of file, `2` invalid key, `3` permanent error, `4` logic error. Lesson 21 covers status codes in depth.

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. STATUS35.

ENVIRONMENT DIVISION.
INPUT-OUTPUT SECTION.
FILE-CONTROL.
    SELECT MISSING-FILE
        ASSIGN TO "no-such-file.dat"
        ORGANIZATION IS LINE SEQUENTIAL
        FILE STATUS IS WS-STATUS.

DATA DIVISION.
FILE SECTION.
FD  MISSING-FILE.
01  MISSING-RECORD    PIC X(80).

WORKING-STORAGE SECTION.
01  WS-STATUS         PIC XX.

PROCEDURE DIVISION.
    OPEN INPUT MISSING-FILE
    IF WS-STATUS NOT = "00"
        DISPLAY "Could not open file, status " WS-STATUS
        MOVE 8 TO RETURN-CODE
    ELSE
        DISPLAY "File opened"
        CLOSE MISSING-FILE
    END-IF
    STOP RUN.
```

```text
Could not open file, status 35
```

Good practice: after each `OPEN`, check the status and stop with a clear message and a non-zero `RETURN-CODE` on failure. A file that quietly failed to open leads to confusing results later.

---

## 14.4 The Classic Read-Process-Write Loop

Nearly every batch program follows this skeleton: open, **prime** the loop with a first read, process records until end of file, then do final work and close.

```text
open files
read first record               <-- the priming read
loop until end of file
    process the record
    write output (if any)
    read next record            <-- the read at the END of the loop
end loop
write totals, close files
```

The read sits at the **bottom** of the loop (after the priming read) so that the end-of-file test happens before the program processes a record that does not exist. The program below reads employees, writes those earning at least 2000 to a new file, and prints counts and a total.

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. FILTER-PAY.

ENVIRONMENT DIVISION.
INPUT-OUTPUT SECTION.
FILE-CONTROL.
    SELECT IN-FILE  ASSIGN TO "pay-in.dat"
        ORGANIZATION IS LINE SEQUENTIAL.
    SELECT OUT-FILE ASSIGN TO "pay-out.dat"
        ORGANIZATION IS LINE SEQUENTIAL.

DATA DIVISION.
FILE SECTION.
FD  IN-FILE.
01  IN-RECORD.
    05  IN-ID         PIC 9(4).
    05  IN-NAME       PIC X(12).
    05  IN-SALARY     PIC 9(5)V99.

FD  OUT-FILE.
01  OUT-RECORD        PIC X(23).

WORKING-STORAGE SECTION.
01  WS-EOF-FLAG       PIC X VALUE "N".
    88  END-OF-INPUT        VALUE "Y".
01  WS-COUNTS.
    05  WS-READ-CNT   PIC 9(3) VALUE 0.
    05  WS-WRITE-CNT  PIC 9(3) VALUE 0.
01  WS-TOTAL-PAY      PIC 9(7)V99 VALUE 0.
01  WS-TOTAL-EDIT     PIC Z,ZZZ,ZZ9.99.

PROCEDURE DIVISION.
0000-MAIN.
    PERFORM 0500-MAKE-TEST-DATA
    PERFORM 1000-INITIALIZE
    PERFORM 2000-PROCESS-RECORD UNTIL END-OF-INPUT
    PERFORM 3000-FINALIZE
    STOP RUN.

0500-MAKE-TEST-DATA.
    OPEN OUTPUT IN-FILE
    MOVE "1001ALVAREZ     0250050" TO IN-RECORD
    WRITE IN-RECORD
    MOVE "1002BAUTISTA    0150000" TO IN-RECORD
    WRITE IN-RECORD
    MOVE "1003CRUZ        0310000" TO IN-RECORD
    WRITE IN-RECORD
    MOVE "1004DIAZ        0099999" TO IN-RECORD
    WRITE IN-RECORD
    CLOSE IN-FILE.

1000-INITIALIZE.
    OPEN INPUT IN-FILE
         OUTPUT OUT-FILE
    PERFORM 2900-READ-NEXT.

2000-PROCESS-RECORD.
    IF IN-SALARY >= 2000
        WRITE OUT-RECORD FROM IN-RECORD
        ADD 1 TO WS-WRITE-CNT
        ADD IN-SALARY TO WS-TOTAL-PAY
    END-IF
    PERFORM 2900-READ-NEXT.

2900-READ-NEXT.
    READ IN-FILE
        AT END
            SET END-OF-INPUT TO TRUE
        NOT AT END
            ADD 1 TO WS-READ-CNT
    END-READ.

3000-FINALIZE.
    CLOSE IN-FILE OUT-FILE
    MOVE WS-TOTAL-PAY TO WS-TOTAL-EDIT
    DISPLAY "Records read:    " WS-READ-CNT
    DISPLAY "Records written: " WS-WRITE-CNT
    DISPLAY "Total pay:       " WS-TOTAL-EDIT.
```

```text
Records read:    004
Records written: 002
Total pay:           5,600.50
```

A few design points worth copying into your own programs:

- Every paragraph has one job, and the main paragraph is just a table of contents (Lesson 12).
- Counters of records read and written are standard control totals. Operations staff compare them against expected numbers to detect lost or duplicated records.
- The input data is created inside the program here only to keep the example self-contained. A real program just reads a file that already exists.
- `WRITE ... FROM` builds the output from a working-storage or input area.
- On the mainframe, you would remove the `ASSIGN TO "literal"` file names and use DD names (`ASSIGN TO INFILE`), and add status checking after each I/O statement.

### Quick reference

| Task | Statement |
|---|---|
| Read all records | Priming read + `PERFORM UNTIL` with `AT END` flag |
| Copy a file | Read, `WRITE out-rec FROM in-rec`, repeat |
| Append to a file | `OPEN EXTEND` |
| Detect a missing file | `FILE STATUS` and test for `35` after `OPEN` |
| Count records | `ADD 1 TO counter` in the loop |

---

[Previous]([13]-String-Handling.m) | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([15]-Indexed-and-Relative-Files.md)