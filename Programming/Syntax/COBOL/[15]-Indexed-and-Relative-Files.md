[Previous]([14]-Sequential-Files.md) | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([16]-Report-Writing.md)

*Files & Data Processing*

# Lesson 15 - Indexed & Relative Files

A sequential file can only be read from the beginning. When a program must find *one* customer or account among millions, it needs a file that can be searched by key. COBOL has two organizations for that: **indexed** files (find a record by a key value) and **relative** files (find a record by its position number).

---

## 15.1 Keys and Access Modes

### Indexed files

An **indexed file** stores records along with an index built on a **record key**, a field inside the record whose value is unique for every record. The runtime uses the index to jump straight to the record you ask for, and it also lets you read the records in key order.

```cobol
SELECT ACCOUNT-FILE
    ASSIGN TO "accounts.idx"
    ORGANIZATION IS INDEXED
    ACCESS MODE IS DYNAMIC
    RECORD KEY IS ACCT-NO
    ALTERNATE RECORD KEY IS ACCT-OWNER WITH DUPLICATES
    FILE STATUS IS WS-STATUS.
```

| Clause | Meaning |
|---|---|
| `ORGANIZATION IS INDEXED` | The file has an index |
| `RECORD KEY IS field` | The primary key. It must be a field in the record description and its values must be **unique** |
| `ALTERNATE RECORD KEY IS field` | An extra key that gives a second way to find records |
| `WITH DUPLICATES` | The alternate key may repeat. Without it, alternate keys must be unique too |
| `ACCESS MODE IS ...` | How the program will reach records (see below) |
| `FILE STATUS IS item` | Receives the two-character result of each operation |

The record layout is an ordinary `FD`:

```cobol
FD  ACCOUNT-FILE.
01  ACCOUNT-RECORD.
    05  ACCT-NO       PIC 9(4).
    05  ACCT-OWNER    PIC X(10).
    05  ACCT-BALANCE  PIC 9(7)V99.
```

> **How it is stored.** GnuCOBOL keeps indexed files using a library such as Berkeley DB (or VBISAM), and may create more than one file on disk for a single indexed file (for example `accounts.idx` and `accounts.idx.1`). Treat them as a unit when you copy or delete the file. On IBM z/OS the equivalent is a **VSAM KSDS** dataset (Lesson 33).

### Relative files

A **relative file** is a set of numbered slots. Record number 1 is the first slot, 2 the second, and so on. The program supplies the slot number in a **relative key** item, which is a numeric data item declared *outside* the record.

```cobol
SELECT ITEM-FILE
    ASSIGN TO "items.rel"
    ORGANIZATION IS RELATIVE
    ACCESS MODE IS DYNAMIC
    RELATIVE KEY IS WS-SLOT
    FILE STATUS IS WS-STATUS.
...
01  WS-SLOT   PIC 9(4).
```

Relative files are simple and fast when your key is already a small whole number (for example product numbers 1 to 5000). They are far less common than indexed files, because most real keys (account numbers, names, codes) are not small consecutive integers.

### Access modes

| `ACCESS MODE` | What you can do |
|---|---|
| `SEQUENTIAL` | Read or write records in order (for indexed files, in key order). The default |
| `RANDOM` | Read, write, rewrite, or delete a record directly by supplying its key. No "next record" |
| `DYNAMIC` | Both: random access by key **and** sequential reading (`READ NEXT`) from a chosen position |

`DYNAMIC` is the most flexible, and it is what the examples below use.

---

## 15.2 `READ`, `REWRITE`, `DELETE`, `START`

All of these use `INVALID KEY` / `NOT INVALID KEY` instead of `AT END` when a problem is about a *key* (not found, duplicate). Use scope terminators (`END-READ`, `END-WRITE`, and so on).

### Creating records: `WRITE`

When writing to an indexed file, set the key field in the record first. `INVALID KEY` runs if a record with that key already exists (status `22`), or if records are being written out of order in sequential access mode (status `21`):

```cobol
MOVE 1001 TO ACCT-NO
MOVE "SANTOS" TO ACCT-OWNER
MOVE 1500.00 TO ACCT-BALANCE
WRITE ACCOUNT-RECORD
    INVALID KEY
        DISPLAY "Duplicate or bad key: " ACCT-NO
END-WRITE
```

### Finding one record: random `READ`

Put the key value into the key field, then `READ`. The whole record is loaded:

```cobol
MOVE 1003 TO ACCT-NO
READ ACCOUNT-FILE
    INVALID KEY
        DISPLAY "Account not found"
    NOT INVALID KEY
        DISPLAY "Owner: " ACCT-OWNER
END-READ
```

To read by the alternate key, say which key to use:

```cobol
MOVE "SANTOS" TO ACCT-OWNER
READ ACCOUNT-FILE KEY IS ACCT-OWNER
    INVALID KEY DISPLAY "No such owner"
END-READ
```

With duplicates allowed, this finds the first record with that value, and `READ NEXT` returns the others.

### Changing a record: `REWRITE`

`REWRITE` replaces the record that was **most recently read** with the current record area. The file must be open `I-O`, and the key must not be changed:

```cobol
READ ACCOUNT-FILE
    INVALID KEY DISPLAY "Not found"
    NOT INVALID KEY
        ADD 250.00 TO ACCT-BALANCE
        REWRITE ACCOUNT-RECORD
            INVALID KEY DISPLAY "Rewrite failed"
        END-REWRITE
END-READ
```

### Removing a record: `DELETE`

`DELETE file-name` removes a record. In random or dynamic access mode, put its key in the key field first. In sequential mode, it deletes the record just read. Note that you name the **file**, not the record:

```cobol
MOVE 1002 TO ACCT-NO
DELETE ACCOUNT-FILE
    INVALID KEY DISPLAY "Nothing to delete"
END-DELETE
```

### Positioning: `START`

`START` positions the file at a key without reading a record. A `READ NEXT` then returns that record and the ones after it, in key order. This is how you process a *range* (all accounts from 2000 upward, or all names beginning with "M"):

```cobol
MOVE 1002 TO ACCT-NO
START ACCOUNT-FILE KEY IS >= ACCT-NO
    INVALID KEY DISPLAY "No record at or above 1002"
END-START
```

The comparison can be `=`, `>`, `>=`, `<`, or `<=`, and the `KEY IS` can name the primary or an alternate key.

### Reading in order: `READ NEXT`

In dynamic or sequential mode, `READ file NEXT` returns the next record in key order and signals the end with `AT END`:

```cobol
READ ACCOUNT-FILE NEXT
    AT END SET END-OF-FILE TO TRUE
    NOT AT END DISPLAY ACCT-NO " " ACCT-OWNER
END-READ
```

### All together

This program builds an indexed file, then demonstrates a random read, `REWRITE`, `DELETE`, a duplicate-key error, `START` with `READ NEXT`, and an alternate-key read.

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. INDEXED-DEMO.

ENVIRONMENT DIVISION.
INPUT-OUTPUT SECTION.
FILE-CONTROL.
    SELECT ACCOUNT-FILE
        ASSIGN TO "accounts.idx"
        ORGANIZATION IS INDEXED
        ACCESS MODE IS DYNAMIC
        RECORD KEY IS ACCT-NO
        ALTERNATE RECORD KEY IS ACCT-OWNER WITH DUPLICATES
        FILE STATUS IS WS-STATUS.

DATA DIVISION.
FILE SECTION.
FD  ACCOUNT-FILE.
01  ACCOUNT-RECORD.
    05  ACCT-NO       PIC 9(4).
    05  ACCT-OWNER    PIC X(10).
    05  ACCT-BALANCE  PIC 9(7)V99.

WORKING-STORAGE SECTION.
01  WS-STATUS         PIC XX.
01  WS-EOF            PIC X VALUE "N".
    88  END-OF-FILE         VALUE "Y".
01  WS-BAL-EDIT       PIC Z,ZZZ,ZZ9.99.

PROCEDURE DIVISION.
0000-MAIN.
    PERFORM 1000-LOAD-FILE
    OPEN I-O ACCOUNT-FILE
    PERFORM 2000-RANDOM-READ
    PERFORM 3000-UPDATE-BALANCE
    PERFORM 4000-DELETE-ACCOUNT
    PERFORM 5000-LIST-FROM-1002
    PERFORM 6000-READ-BY-OWNER
    CLOSE ACCOUNT-FILE
    STOP RUN.

1000-LOAD-FILE.
    OPEN OUTPUT ACCOUNT-FILE
    MOVE 1001 TO ACCT-NO
    MOVE "SANTOS"  TO ACCT-OWNER
    MOVE 1500.00   TO ACCT-BALANCE
    WRITE ACCOUNT-RECORD
    MOVE 1002 TO ACCT-NO
    MOVE "REYES"   TO ACCT-OWNER
    MOVE 320.75    TO ACCT-BALANCE
    WRITE ACCOUNT-RECORD
    MOVE 1003 TO ACCT-NO
    MOVE "SANTOS"  TO ACCT-OWNER
    MOVE 90.10     TO ACCT-BALANCE
    WRITE ACCOUNT-RECORD
    MOVE 1004 TO ACCT-NO
    MOVE "CRUZ"    TO ACCT-OWNER
    MOVE 5000.00   TO ACCT-BALANCE
    WRITE ACCOUNT-RECORD
    MOVE 1003 TO ACCT-NO
    MOVE "DUPLICATE" TO ACCT-OWNER
    MOVE 1.00      TO ACCT-BALANCE
    WRITE ACCOUNT-RECORD
        INVALID KEY
            DISPLAY "Write rejected, status " WS-STATUS
    END-WRITE
    CLOSE ACCOUNT-FILE.

2000-RANDOM-READ.
    MOVE 1003 TO ACCT-NO
    READ ACCOUNT-FILE
        INVALID KEY
            DISPLAY "1003 not found"
        NOT INVALID KEY
            MOVE ACCT-BALANCE TO WS-BAL-EDIT
            DISPLAY "Read 1003: " ACCT-OWNER WS-BAL-EDIT
    END-READ.

3000-UPDATE-BALANCE.
    MOVE 1001 TO ACCT-NO
    READ ACCOUNT-FILE
        INVALID KEY
            DISPLAY "1001 not found"
        NOT INVALID KEY
            ADD 250.00 TO ACCT-BALANCE
            REWRITE ACCOUNT-RECORD
                INVALID KEY DISPLAY "Rewrite failed"
            END-REWRITE
            MOVE ACCT-BALANCE TO WS-BAL-EDIT
            DISPLAY "Updated 1001: " WS-BAL-EDIT
    END-READ.

4000-DELETE-ACCOUNT.
    MOVE 1002 TO ACCT-NO
    DELETE ACCOUNT-FILE
        INVALID KEY
            DISPLAY "1002 not found"
        NOT INVALID KEY
            DISPLAY "Deleted 1002"
    END-DELETE.

5000-LIST-FROM-1002.
    DISPLAY "Accounts from key 1002 upward:"
    MOVE 1002 TO ACCT-NO
    MOVE "N" TO WS-EOF
    START ACCOUNT-FILE KEY IS >= ACCT-NO
        INVALID KEY
            SET END-OF-FILE TO TRUE
    END-START
    PERFORM UNTIL END-OF-FILE
        READ ACCOUNT-FILE NEXT
            AT END
                SET END-OF-FILE TO TRUE
            NOT AT END
                MOVE ACCT-BALANCE TO WS-BAL-EDIT
                DISPLAY "  " ACCT-NO " " ACCT-OWNER WS-BAL-EDIT
        END-READ
    END-PERFORM.

6000-READ-BY-OWNER.
    MOVE "SANTOS" TO ACCT-OWNER
    READ ACCOUNT-FILE KEY IS ACCT-OWNER
        INVALID KEY
            DISPLAY "No SANTOS found"
        NOT INVALID KEY
            DISPLAY "First SANTOS record is account " ACCT-NO
    END-READ.
```

```text
Write rejected, status 22
Read 1003: SANTOS           90.10
Updated 1001:     1,750.00
Deleted 1002
Accounts from key 1002 upward:
  1003 SANTOS           90.10
  1004 CRUZ          5,000.00
First SANTOS record is account 1001
```

A few details to notice:

- The duplicate key produced status `22`, and the existing record `1003` was not harmed.
- After `DELETE` of 1002, `START ... >= 1002` positioned on the next record that exists (1003), which is why 1002 does not appear in the list.
- The alternate key `ACCT-OWNER` found account 1001 as the *first* `SANTOS`, because duplicates are returned in the order they were added.

### Relative file example

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. RELATIVE-DEMO.

ENVIRONMENT DIVISION.
INPUT-OUTPUT SECTION.
FILE-CONTROL.
    SELECT ITEM-FILE
        ASSIGN TO "items.rel"
        ORGANIZATION IS RELATIVE
        ACCESS MODE IS DYNAMIC
        RELATIVE KEY IS WS-SLOT
        FILE STATUS IS WS-STATUS.

DATA DIVISION.
FILE SECTION.
FD  ITEM-FILE.
01  ITEM-RECORD.
    05  ITEM-NAME    PIC X(10).
    05  ITEM-QTY     PIC 9(4).

WORKING-STORAGE SECTION.
01  WS-SLOT          PIC 9(4).
01  WS-STATUS        PIC XX.

PROCEDURE DIVISION.
    OPEN OUTPUT ITEM-FILE
    MOVE 3 TO WS-SLOT
    MOVE "BOLTS" TO ITEM-NAME
    MOVE 250 TO ITEM-QTY
    WRITE ITEM-RECORD
        INVALID KEY DISPLAY "Write failed " WS-STATUS
    END-WRITE
    MOVE 7 TO WS-SLOT
    MOVE "NUTS" TO ITEM-NAME
    MOVE 90 TO ITEM-QTY
    WRITE ITEM-RECORD
    CLOSE ITEM-FILE

    OPEN INPUT ITEM-FILE
    MOVE 7 TO WS-SLOT
    READ ITEM-FILE
        INVALID KEY DISPLAY "Slot 7 empty"
        NOT INVALID KEY DISPLAY "Slot 7: " ITEM-NAME ITEM-QTY
    END-READ
    MOVE 5 TO WS-SLOT
    READ ITEM-FILE
        INVALID KEY DISPLAY "Slot 5 empty, status " WS-STATUS
    END-READ
    CLOSE ITEM-FILE
    STOP RUN.
```

```text
Slot 7: NUTS      0090
Slot 5 empty, status 23
```

Slots can be filled in any order and left empty. Reading an empty slot gives `INVALID KEY` with status `23`.

---

## 15.3 Random vs Sequential vs Dynamic Access

The access mode you choose depends on how the program uses the file:

| Mode | Typical job | Operations available |
|---|---|---|
| `SEQUENTIAL` | Batch report over the whole file, or loading a new file in key order | `READ [NEXT]`, `WRITE` (keys must ascend), `REWRITE`, `DELETE` after a read |
| `RANDOM` | Online lookups and updates, such as "get this one account" | `READ` by key, `WRITE`, `REWRITE`, `DELETE`, `START` is not allowed |
| `DYNAMIC` | Look up a starting point, then scan forward (browsing, range reports) | Everything above, including `START` and `READ NEXT` |

### Which organization should you choose?

| Need | Choose |
|---|---|
| Process every record once, in order | **Sequential** (simplest and fastest) |
| Find or update individual records by an identifier | **Indexed** |
| Find records by two or more different fields | **Indexed** with alternate keys |
| Keys are small consecutive integers | **Relative** |
| Read a file and also add new keys in the middle | **Indexed** (sequential files cannot insert) |

### Practical advice

- **Load in key order.** When you first create an indexed file in sequential mode, records must arrive in ascending primary-key order, or you will get status `21`. If your input is unsorted, sort it first (Lesson 17), or open the file in `RANDOM` or `DYNAMIC` mode when loading.
- **Always check `FILE STATUS`.** `INVALID KEY` covers key problems, but other failures (a missing file or locked data) need the status code (Lesson 21).
- **Do not change the primary key** of a record you intend to `REWRITE`. To change a key, `DELETE` the record and `WRITE` a new one.
- **Space is not reclaimed automatically** in some implementations. Periodically unload and reload large indexed files.
- **`REWRITE` and `DELETE` depend on the last read.** In sequential access, the record to change is the one most recently read. A failed read leaves no current record.

> **Mainframe note.** Indexed files on z/OS are VSAM KSDS data sets, relative files are VSAM RRDS data sets, and the COBOL statements are the same as above. The file must be created first with the `IDCAMS` utility (Lesson 31), and the JCL `DD` statement connects it to the program.

---

[Previous]([14]-Sequential-Files.md) | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([16]-Report-Writing.md)