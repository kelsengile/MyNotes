[Previous](./[16]-Report-Writing.md) | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next](./[18]-Control-Breaks-and-Batch.md)

*Files & Data Processing*

# Lesson 17 - Sorting & Merging (`SORT`, `MERGE`)

Files rarely arrive in the order a program needs them. Control-break reports (Lesson 18) need records grouped by department, a master-file update needs two files in the same key order, and a customer list is easier to read alphabetically. COBOL solves all of this with two built-in statements: `SORT`, which puts records in order, and `MERGE`, which combines files that are already in order.

---

## 17.1 `SORT` with `USING` and `GIVING`

### What `SORT` does

`SORT` reads records from one or more input files, orders them by one or more **keys**, and writes the ordered records to an output file. You do not write the sorting algorithm. The runtime library (or, on the mainframe, the installation's sort product) does the work, using temporary disk space when the data is too big for memory.

A sort involves three files:

```text
input file(s)  --->  [ sort work file ]  --->  output file(s)
 (USING)              (the SD file)              (GIVING)
```

The **sort work file** is a temporary file that exists only while the `SORT` statement runs. You never `OPEN`, `READ`, or `CLOSE` it yourself.

### Declaring the sort file: `SELECT` and `SD`

The sort file is declared like any other file, with two differences. It has a `SELECT` entry that needs no `ORGANIZATION`, and it is described with an **`SD`** (sort description) instead of an `FD` (file description).

```cobol
ENVIRONMENT DIVISION.
INPUT-OUTPUT SECTION.
FILE-CONTROL.
    SELECT SORT-FILE ASSIGN TO "sortwork".

DATA DIVISION.
FILE SECTION.
SD  SORT-FILE.
01  SORT-RECORD.
    05  SR-ID         PIC 9(4).
    05  SR-NAME       PIC X(10).
    05  SR-DEPT       PIC X(3).
    05  SR-SALARY     PIC 9(5).
```

| | `FD` | `SD` |
|---|---|---|
| Describes | A real file on disk | The temporary sort work file |
| Opened by | Your `OPEN` statement | The `SORT` statement itself |
| Record layout | Required | Required, and it must contain every **key** field |
| `BLOCK CONTAINS`, `RECORDING MODE`, etc. | Allowed | Not used |

> **Mainframe note.** `ASSIGN TO SORTWK01` names a DD name, and the JCL provides the work space. On z/OS the actual sorting is done by a sort product such as DFSORT or SyncSort, which is why mainframe `SORT` is so fast on huge files (Lesson 31).

### The `SORT` statement

```text
SORT sort-file
    ON {ASCENDING | DESCENDING} KEY key-1 [key-2 ...]
    [ON {ASCENDING | DESCENDING} KEY key-3 ...]
    [WITH DUPLICATES IN ORDER]
    [COLLATING SEQUENCE IS alphabet-name]
    USING input-file-1 [input-file-2 ...]
    GIVING output-file-1 [output-file-2 ...]
```

| Part | Meaning |
|---|---|
| `ON ASCENDING KEY` | Smallest first (A to Z, 0 to 9) |
| `ON DESCENDING KEY` | Largest first |
| Several keys | Listed from **major** (most important) to **minor**. Records are ordered by the first key, and ties are broken by the next key, and so on |
| `USING file` | Read every record of this file into the sort. The sort opens and closes the file for you |
| `GIVING file` | Write the ordered records to this file. The sort opens and closes it for you |
| `WITH DUPLICATES IN ORDER` | Records with identical keys stay in the order they were read |
| `COLLATING SEQUENCE` | Compare alphanumeric keys using a different character order (Lesson 24) |

The key names are fields of the **sort record** (the `SD`), not of the input or output file records. The input and output files can use a single `PIC X(n)` record, as long as the record is the same length as the sort record and the bytes line up.

### A first example

This program writes five employee records in random order, sorts them by department (ascending) and, within each department, by salary (highest first), and then lists the result.

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. SORT1.

ENVIRONMENT DIVISION.
INPUT-OUTPUT SECTION.
FILE-CONTROL.
    SELECT IN-FILE   ASSIGN TO "emp-in.dat"
        ORGANIZATION IS LINE SEQUENTIAL.
    SELECT OUT-FILE  ASSIGN TO "emp-sorted.dat"
        ORGANIZATION IS LINE SEQUENTIAL.
    SELECT SORT-FILE ASSIGN TO "sortwork".

DATA DIVISION.
FILE SECTION.
FD  IN-FILE.
01  IN-RECORD         PIC X(22).

FD  OUT-FILE.
01  OUT-RECORD        PIC X(22).

SD  SORT-FILE.
01  SORT-RECORD.
    05  SR-ID         PIC 9(4).
    05  SR-NAME       PIC X(10).
    05  SR-DEPT       PIC X(3).
    05  SR-SALARY     PIC 9(5).

WORKING-STORAGE SECTION.
01  WS-EOF            PIC X VALUE "N".
    88  END-OF-FILE         VALUE "Y".

PROCEDURE DIVISION.
0000-MAIN.
    PERFORM 1000-MAKE-TEST-DATA
    SORT SORT-FILE
        ON ASCENDING KEY SR-DEPT
        ON DESCENDING KEY SR-SALARY
        USING IN-FILE
        GIVING OUT-FILE
    IF SORT-RETURN NOT = 0
        DISPLAY "Sort failed, SORT-RETURN = " SORT-RETURN
        MOVE 8 TO RETURN-CODE
    ELSE
        PERFORM 2000-SHOW-RESULT
    END-IF
    STOP RUN.

1000-MAKE-TEST-DATA.
    OPEN OUTPUT IN-FILE
    MOVE "1001ALVAREZ   HR 02500" TO IN-RECORD
    WRITE IN-RECORD
    MOVE "1002BAUTISTA  IT 03100" TO IN-RECORD
    WRITE IN-RECORD
    MOVE "1003CRUZ      HR 03000" TO IN-RECORD
    WRITE IN-RECORD
    MOVE "1004DIAZ      FIN01800" TO IN-RECORD
    WRITE IN-RECORD
    MOVE "1005ESTRADA   IT 02800" TO IN-RECORD
    WRITE IN-RECORD
    CLOSE IN-FILE.

2000-SHOW-RESULT.
    OPEN INPUT OUT-FILE
    PERFORM UNTIL END-OF-FILE
        READ OUT-FILE
            AT END
                SET END-OF-FILE TO TRUE
            NOT AT END
                DISPLAY OUT-RECORD
        END-READ
    END-PERFORM
    CLOSE OUT-FILE.
```

The input file `emp-in.dat` holds the records in the order they were written:

```text
1001ALVAREZ   HR 02500
1002BAUTISTA  IT 03100
1003CRUZ      HR 03000
1004DIAZ      FIN01800
1005ESTRADA   IT 02800
```

Running the program prints the sorted file:

```text
1004DIAZ      FIN01800
1003CRUZ      HR 03000
1001ALVAREZ   HR 02500
1002BAUTISTA  IT 03100
1005ESTRADA   IT 02800
```

Notice the order: `FIN` comes before `HR` and `IT`, and inside `HR` the salary 3000 comes before 2500, because the second key is `DESCENDING`.

### Rules worth remembering

- **Keys must be fields of the `SD` record**, and each must lie wholly inside it. A key can be a group item (it is then compared as text) or an elementary item.
- **Numeric keys compare by value**, so `PIC 9(5)` salary `02500` is less than `03000`. A `PIC X` key holding digits compares character by character. Use `PIC 9` fields for numbers.
- **Alphanumeric keys compare by the collating sequence.** In ASCII, uppercase letters sort before lowercase, and digits before letters. In EBCDIC the order is different (Lesson 24).
- **The files named in `USING` and `GIVING` must be closed** when the `SORT` runs. If you opened one earlier, `CLOSE` it first.
- **Without `WITH DUPLICATES IN ORDER`**, records with equal keys may come out in any order. Add the phrase whenever the original order matters, for example when a file is sorted by date and you now sort by customer but want each customer's records to stay in date order.
- **Check `SORT-RETURN`.** This special register holds `0` after a successful sort and a non-zero value (`16` for a failure) if the sort could not complete. The example tests it before reading the result. A job that sorts a file and carries on after a failure produces wrong reports.
- You can name several files after `USING` (their records are all sorted together) and after `GIVING` (each receives a full copy of the sorted data).

---

## 17.2 Input and Output Procedures

`USING` and `GIVING` sort every record of the input and write every record of the output. Often you want more control: skip records you do not need, reformat them, count them, or print them directly without creating an intermediate file. COBOL lets you replace either half with your own code.

| Replace | With | Your code uses |
|---|---|---|
| `USING file` | `INPUT PROCEDURE IS name` | `RELEASE` to hand records to the sort |
| `GIVING file` | `OUTPUT PROCEDURE IS name` | `RETURN` to take sorted records back |

You can mix them freely: `USING` with `OUTPUT PROCEDURE`, `INPUT PROCEDURE` with `GIVING`, or procedures on both sides.

### `RELEASE` and `RETURN`

```text
RELEASE sort-record [FROM identifier]

RETURN sort-file [INTO identifier]
    AT END statements
    [NOT AT END statements]
END-RETURN
```

- `RELEASE` is the sort's equivalent of `WRITE`. It copies the current sort record (the `SD` record) into the sort. It uses the **record name**, like `WRITE`.
- `RETURN` is the equivalent of `READ`. It takes the next record, in sorted order, out of the sort and puts it in the `SD` record area. `AT END` fires after the last record has been returned. Use `RETURN` with the **file name**.
- `RELEASE` is legal only inside an input procedure, and `RETURN` only inside an output procedure.

### How the two procedures fit around the sort

```text
INPUT PROCEDURE                    sort runs                OUTPUT PROCEDURE
open input files          RELEASE ----------> RETURN        loop: RETURN next record
read, select, reformat    (records go into    (records come   handle the record
RELEASE each record        the sort)           out in order)  until AT END
close input files
```

The input procedure runs to completion first. Only then does the sorting happen, and finally the output procedure runs. This means an output procedure can never see a record before every input record has been released.

### Example: filter, sort, and print without a work file

This program keeps only employees earning at least 2000, sorts them by department and name, and prints them directly from the output procedure. Notice that the sort record (`SORT-RECORD`) is *not* the same layout as the input record. The input procedure builds it from selected fields.

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. SORT2.

ENVIRONMENT DIVISION.
INPUT-OUTPUT SECTION.
FILE-CONTROL.
    SELECT IN-FILE   ASSIGN TO "emp-in.dat"
        ORGANIZATION IS LINE SEQUENTIAL.
    SELECT SORT-FILE ASSIGN TO "sortwork".

DATA DIVISION.
FILE SECTION.
FD  IN-FILE.
01  IN-RECORD.
    05  IN-ID         PIC 9(4).
    05  IN-NAME       PIC X(10).
    05  IN-DEPT       PIC X(3).
    05  IN-SALARY     PIC 9(5).

SD  SORT-FILE.
01  SORT-RECORD.
    05  SR-DEPT       PIC X(3).
    05  SR-NAME       PIC X(10).
    05  SR-SALARY     PIC 9(5).

WORKING-STORAGE SECTION.
01  WS-IN-EOF         PIC X VALUE "N".
    88  END-OF-INPUT        VALUE "Y".
01  WS-SORT-EOF       PIC X VALUE "N".
    88  END-OF-SORTED       VALUE "Y".
01  WS-SELECTED       PIC 99 VALUE 0.
01  WS-SAL-EDIT       PIC ZZ,ZZ9.

PROCEDURE DIVISION.
0000-MAIN SECTION.
    PERFORM 0500-MAKE-TEST-DATA
    SORT SORT-FILE
        ON ASCENDING KEY SR-DEPT SR-NAME
        WITH DUPLICATES IN ORDER
        INPUT PROCEDURE IS 1000-SELECT-RECORDS
        OUTPUT PROCEDURE IS 2000-PRINT-RECORDS
    DISPLAY "Records sorted: " WS-SELECTED
    STOP RUN.

0500-MAKE-TEST-DATA SECTION.
    OPEN OUTPUT IN-FILE
    MOVE "1001ALVAREZ   HR 02500" TO IN-RECORD
    WRITE IN-RECORD
    MOVE "1002BAUTISTA  IT 03100" TO IN-RECORD
    WRITE IN-RECORD
    MOVE "1003CRUZ      HR 03000" TO IN-RECORD
    WRITE IN-RECORD
    MOVE "1004DIAZ      FIN01800" TO IN-RECORD
    WRITE IN-RECORD
    MOVE "1005ESTRADA   IT 02800" TO IN-RECORD
    WRITE IN-RECORD
    CLOSE IN-FILE.

1000-SELECT-RECORDS SECTION.
    OPEN INPUT IN-FILE
    PERFORM 1100-READ-INPUT
    PERFORM UNTIL END-OF-INPUT
        IF IN-SALARY >= 2000
            MOVE IN-DEPT   TO SR-DEPT
            MOVE IN-NAME   TO SR-NAME
            MOVE IN-SALARY TO SR-SALARY
            RELEASE SORT-RECORD
            ADD 1 TO WS-SELECTED
        END-IF
        PERFORM 1100-READ-INPUT
    END-PERFORM
    CLOSE IN-FILE.

1100-READ-INPUT SECTION.
    READ IN-FILE
        AT END SET END-OF-INPUT TO TRUE
    END-READ.

2000-PRINT-RECORDS SECTION.
    PERFORM 2100-RETURN-NEXT
    PERFORM UNTIL END-OF-SORTED
        MOVE SR-SALARY TO WS-SAL-EDIT
        DISPLAY SR-DEPT " " SR-NAME " " WS-SAL-EDIT
        PERFORM 2100-RETURN-NEXT
    END-PERFORM.

2100-RETURN-NEXT SECTION.
    RETURN SORT-FILE
        AT END SET END-OF-SORTED TO TRUE
    END-RETURN.
```

Output:

```text
HR  ALVAREZ     2,500
HR  CRUZ        3,000
IT  BAUTISTA    3,100
IT  ESTRADA     2,800
Records sorted: 04
```

Employee `DIAZ` (salary 1800) was filtered out by the input procedure, so only four records were sorted.

### Writing the procedures

An input or output procedure is a **section**, or a range of paragraphs named with `THRU`:

```cobol
SORT SORT-FILE
    ON ASCENDING KEY SR-DEPT
    INPUT PROCEDURE IS 1000-SELECT-START THRU 1000-SELECT-EXIT
    OUTPUT PROCEDURE IS 2000-PRINT-RECORDS
```

Rules to follow:

- **Your code opens, reads, and closes the files** that feed `RELEASE`. In the example, `IN-FILE` is opened and closed inside the input procedure, and `END-OF-INPUT` is a flag that the procedure sets itself.
- **Control must flow through the whole procedure.** The sort runs the section (or paragraph range) from beginning to end. Do not `GO TO` a paragraph outside it, and do not `STOP RUN` from inside it.
- **Do not use `SORT` or `MERGE` inside a procedure** that belongs to another sort.
- **Mind the fall-through trap from Lesson 12.** `PERFORM` of a paragraph that sits inside a section would run it twice. The example avoids this by making each routine, even `1100-READ-INPUT` and `2100-RETURN-NEXT`, a section of its own. A program that uses sections must put **all** of its procedure division code in sections, which is why `0000-MAIN` and the test-data builder are sections too.

### What procedures are used for

| Need | Where |
|---|---|
| Drop records (inactive customers, zero balances) | Input procedure: simply do not `RELEASE` them |
| Reformat or shorten records before the sort | Input procedure: `MOVE` fields into the `SD` record |
| Validate and count rejects | Input procedure |
| Add computed fields (for example a full name) | Input procedure |
| Print sorted data without writing an intermediate file | Output procedure |
| Remove duplicates after sorting | Output procedure: compare each record with the previous one |
| Compute totals or control breaks on sorted data | Output procedure (Lesson 18) |

Sorting fewer, shorter records is faster, so filtering in the input procedure usually pays for itself.

> **Mainframe note.** For a plain sort with no special logic, many shops skip COBOL and use a JCL step that runs the sort utility directly (Lesson 31). COBOL `SORT` with procedures is used when business logic must run during the sort.

---

## 17.3 `MERGE`

`MERGE` combines two or more files **that are already sorted on the same keys** into one sorted file. It is much cheaper than `SORT`, because it does not reorder anything. It just repeatedly takes the smallest next record from the front of each input.

```text
MERGE merge-file
    ON {ASCENDING | DESCENDING} KEY key-1 [key-2 ...]
    [COLLATING SEQUENCE IS alphabet-name]
    USING input-file-1 input-file-2 [input-file-3 ...]
    {GIVING output-file | OUTPUT PROCEDURE IS name}
```

The merge file is declared with `SELECT` and `SD`, exactly like a sort file. The differences from `SORT` are:

| | `SORT` | `MERGE` |
|---|---|---|
| Input files | Any order | **Must already be sorted** on the merge keys |
| Number of inputs | One or more | **Two or more** |
| `INPUT PROCEDURE` | Allowed | Not allowed (only `USING`) |
| Output | `GIVING` or `OUTPUT PROCEDURE` | `GIVING` or `OUTPUT PROCEDURE` |
| Work | Full sort | One pass over the inputs |

### Example: combining two branches' daily sales

Each branch sends a file already sorted by date. The head office program merges them into one file in date order.

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. MERGE1.

ENVIRONMENT DIVISION.
INPUT-OUTPUT SECTION.
FILE-CONTROL.
    SELECT BRANCH-A   ASSIGN TO "branch-a.dat"
        ORGANIZATION IS LINE SEQUENTIAL.
    SELECT BRANCH-B   ASSIGN TO "branch-b.dat"
        ORGANIZATION IS LINE SEQUENTIAL.
    SELECT ALL-SALES  ASSIGN TO "all-sales.dat"
        ORGANIZATION IS LINE SEQUENTIAL.
    SELECT MERGE-FILE ASSIGN TO "mergework".

DATA DIVISION.
FILE SECTION.
FD  BRANCH-A.
01  A-RECORD          PIC X(14).

FD  BRANCH-B.
01  B-RECORD          PIC X(14).

FD  ALL-SALES.
01  ALL-RECORD        PIC X(14).

SD  MERGE-FILE.
01  MERGE-RECORD.
    05  MG-DATE       PIC 9(8).
    05  MG-BRANCH     PIC X.
    05  MG-AMOUNT     PIC 9(5).

WORKING-STORAGE SECTION.
01  WS-EOF            PIC X VALUE "N".
    88  END-OF-FILE         VALUE "Y".

PROCEDURE DIVISION.
0000-MAIN.
    PERFORM 1000-MAKE-TEST-DATA
    MERGE MERGE-FILE
        ON ASCENDING KEY MG-DATE
        USING BRANCH-A BRANCH-B
        GIVING ALL-SALES
    PERFORM 2000-SHOW-RESULT
    STOP RUN.

1000-MAKE-TEST-DATA.
    OPEN OUTPUT BRANCH-A
    MOVE "20261001A01200" TO A-RECORD
    WRITE A-RECORD
    MOVE "20261003A00450" TO A-RECORD
    WRITE A-RECORD
    MOVE "20261005A00980" TO A-RECORD
    WRITE A-RECORD
    CLOSE BRANCH-A
    OPEN OUTPUT BRANCH-B
    MOVE "20261002B00700" TO B-RECORD
    WRITE B-RECORD
    MOVE "20261003B01100" TO B-RECORD
    WRITE B-RECORD
    CLOSE BRANCH-B.

2000-SHOW-RESULT.
    OPEN INPUT ALL-SALES
    PERFORM UNTIL END-OF-FILE
        READ ALL-SALES
            AT END
                SET END-OF-FILE TO TRUE
            NOT AT END
                DISPLAY ALL-RECORD
        END-READ
    END-PERFORM
    CLOSE ALL-SALES.
```

Output:

```text
20261001A01200
20261002B00700
20261003A00450
20261003B01100
20261005A00980
```

Look at 3 October: branch A's record comes before branch B's. When keys are equal, `MERGE` takes records from the files in the **order the files are named after `USING`**, so the merge is predictable.

### Merge with an output procedure

As with `SORT`, you can replace `GIVING` with an output procedure and `RETURN` the merged records one at a time, for example to total sales per day while writing no intermediate file:

```cobol
MERGE MERGE-FILE
    ON ASCENDING KEY MG-DATE
    USING BRANCH-A BRANCH-B
    OUTPUT PROCEDURE IS 2000-DAILY-TOTALS
```

### Common mistakes

- **Merging files that are not sorted.** The merge does not sort anything, and it may not detect the problem, so the output can be silently out of order. Sort each input first, or have the process that creates it guarantee the order.
- **Different key definitions.** The keys, their order (ascending or descending), and the collating sequence must match how each input was sorted.
- **Mismatched record layouts.** All input files and the merge file are expected to have records of the same layout and compatible length.

### SORT or MERGE?

| Situation | Use |
|---|---|
| One unsorted file | `SORT` |
| Several unsorted files that must end up in one order | `SORT ... USING file-1 file-2` |
| Several files that are each already sorted | `MERGE` |
| A big history file plus a small sorted file of new records | `MERGE` (cheaper than re-sorting everything) |
| Two files with *different* layouts that must be combined by key | Neither: use the match/merge technique in Lesson 18 |

A typical overnight pattern is to `SORT` each day's raw transaction file by account number, then `MERGE` the sorted files from several regions, and finally feed the result to a control-break report or a master-file update (Lesson 18).

---

[Previous](./[16]-Report-Writing.md) | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next](./[18]-Control-Breaks-and-Batch.md)
