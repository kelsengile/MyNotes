[Previous](./[17]-Sorting-and-Merging.md) | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next](./[19]-Subprograms.md)

*Files & Data Processing*

# Lesson 18 - Control Breaks & Batch Processing Patterns

Batch programs follow a small number of well-worn patterns, and two of them appear in almost every business system: the **control break**, which produces subtotals whenever a key changes, and the **master-file update** (also called match/merge), which applies a file of changes to a master file. This lesson builds both from scratch. You have already seen the building blocks: the read loop (Lesson 14), edited pictures for reports (Lessons 8 and 16), and sorting (Lesson 17).
s
---

## 18.1 What Is a Control Break?

A **control break** happens when the value of a **control field** (the key that groups the records) changes from one record to the next. At that moment the program stops, prints a subtotal for the group that just ended, resets its accumulators, and starts the next group.

Here is a department payroll listing. Each time the department changes, the program prints that department's total:

```text
DEPT  NAME        SALARY
HR    CRUZ        3,000
HR    ALVAREZ     2,500
     ** HR  total:     5,500        <-- control break on DEPT
IT    BAUTISTA    3,100
IT    ESTRADA     2,800
     ** IT  total:     5,900        <-- control break on DEPT
```

Control breaks produce the subtotals, group counts, and grand totals in most business reports: sales by region, claims by policy type, orders by customer.

### The input must be sorted

A control break compares each record with the one before it. If the file is not sorted by the control field, the same department appears in several separate pieces, and the program prints several subtotals for it. **Sort first** (Lesson 17):

```cobol
SORT SORT-FILE
    ON ASCENDING KEY SR-DEPT
    WITH DUPLICATES IN ORDER
    USING EMP-IN
    GIVING EMP-BY-DEPT
```

Report Writer (Lesson 16) can automate control breaks, but it has the same requirement and is not available on IBM mainframes. Writing the logic by hand is the skill that transfers everywhere.

### The algorithm

```text
open files
read the first record                         (priming read)
if there is one: save its key as the "previous key"
loop until end of file
    if this record's key is different from the previous key
        CONTROL BREAK:
            print the subtotal for the group that just ended
            add the subtotal to the next level up (grand total)
            reset the group accumulators to zero
            save the new key as the previous key
    process the record (print the detail line, add to the accumulators)
    read the next record
end loop
final control break: print the last group's subtotal      <-- easy to forget
print grand totals
close files
```

### The four classic mistakes

| Mistake | Symptom | Fix |
|---|---|---|
| Input not sorted by the control field | Several subtotals for the same key | Sort first, with the control field as the major key |
| Forgetting the final break | The **last** group has no subtotal, and the grand total is short | Perform the break paragraph once more after the loop |
| Comparing with an unset previous key on the first record | A bogus empty subtotal printed before the first group | Save the first record's key after the priming read, before the loop |
| Resetting accumulators before printing them, or saving the new key too early | Subtotals of zero, or labeled with the next group's key | Print, then add to the next level, then reset, then save the new key |

The order inside the break paragraph matters. Print and roll up the old group's numbers while the old key and totals are still in place, and only then reset and move to the new key. Notice also that the break is detected **before** the new record is added to any total. At that point the record in the buffer belongs to the *next* group.

---

## 18.2 Single and Multi-Level Totals

### A single-level control break

The program below reads employees already sorted by department. For each department it prints a count and a salary total, and at the end it prints grand totals. It follows the structure from Lesson 12: a table-of-contents main paragraph, an initialize paragraph, one paragraph per job, and a single `READ` paragraph.

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. CBREAK1.

ENVIRONMENT DIVISION.
INPUT-OUTPUT SECTION.
FILE-CONTROL.
    SELECT EMP-FILE ASSIGN TO "emp-by-dept.dat"
        ORGANIZATION IS LINE SEQUENTIAL.

DATA DIVISION.
FILE SECTION.
FD  EMP-FILE.
01  EMP-RECORD.
    05  EMP-DEPT      PIC X(3).
    05  EMP-NAME      PIC X(10).
    05  EMP-SALARY    PIC 9(5).

WORKING-STORAGE SECTION.
01  WS-EOF            PIC X VALUE "N".
    88  END-OF-FILE         VALUE "Y".
01  WS-PREV-DEPT      PIC X(3) VALUE SPACES.
01  WS-DEPT-COUNT     PIC 999  VALUE 0.
01  WS-DEPT-TOTAL     PIC 9(7) VALUE 0.
01  WS-GRAND-COUNT    PIC 999  VALUE 0.
01  WS-GRAND-TOTAL    PIC 9(8) VALUE 0.
01  WS-SAL-EDIT       PIC ZZ,ZZ9.
01  WS-TOT-EDIT       PIC Z,ZZZ,ZZ9.

PROCEDURE DIVISION.
0000-MAIN.
    PERFORM 0500-MAKE-TEST-DATA
    PERFORM 1000-INITIALIZE
    PERFORM 2000-PROCESS-RECORD UNTIL END-OF-FILE
    PERFORM 3000-FINALIZE
    STOP RUN.

0500-MAKE-TEST-DATA.
    OPEN OUTPUT EMP-FILE
    MOVE "FINDIAZ      01800" TO EMP-RECORD
    WRITE EMP-RECORD
    MOVE "HR CRUZ      03000" TO EMP-RECORD
    WRITE EMP-RECORD
    MOVE "HR ALVAREZ   02500" TO EMP-RECORD
    WRITE EMP-RECORD
    MOVE "IT BAUTISTA  03100" TO EMP-RECORD
    WRITE EMP-RECORD
    MOVE "IT ESTRADA   02800" TO EMP-RECORD
    WRITE EMP-RECORD
    MOVE "IT FLORES    02200" TO EMP-RECORD
    WRITE EMP-RECORD
    CLOSE EMP-FILE.

1000-INITIALIZE.
    OPEN INPUT EMP-FILE
    PERFORM 2900-READ-NEXT
    IF NOT END-OF-FILE
        MOVE EMP-DEPT TO WS-PREV-DEPT
        DISPLAY "DEPT  NAME        SALARY"
    END-IF.

2000-PROCESS-RECORD.
    IF EMP-DEPT NOT = WS-PREV-DEPT
        PERFORM 2500-DEPT-BREAK
    END-IF
    MOVE EMP-SALARY TO WS-SAL-EDIT
    DISPLAY EMP-DEPT "   " EMP-NAME " " WS-SAL-EDIT
    ADD 1          TO WS-DEPT-COUNT
    ADD EMP-SALARY TO WS-DEPT-TOTAL
    PERFORM 2900-READ-NEXT.

2500-DEPT-BREAK.
    MOVE WS-DEPT-TOTAL TO WS-TOT-EDIT
    DISPLAY "     ** " WS-PREV-DEPT " total (" WS-DEPT-COUNT "): "
            WS-TOT-EDIT
    DISPLAY " "
    ADD WS-DEPT-COUNT TO WS-GRAND-COUNT
    ADD WS-DEPT-TOTAL TO WS-GRAND-TOTAL
    MOVE 0 TO WS-DEPT-COUNT WS-DEPT-TOTAL
    MOVE EMP-DEPT TO WS-PREV-DEPT.

2900-READ-NEXT.
    READ EMP-FILE
        AT END SET END-OF-FILE TO TRUE
    END-READ.

3000-FINALIZE.
    IF WS-GRAND-COUNT > 0 OR WS-DEPT-COUNT > 0
        PERFORM 2500-DEPT-BREAK
        MOVE WS-GRAND-TOTAL TO WS-TOT-EDIT
        DISPLAY "     ** GRAND total (" WS-GRAND-COUNT "): "
                WS-TOT-EDIT
    ELSE
        DISPLAY "No records to report"
    END-IF
    CLOSE EMP-FILE.
```

Output:

```text
DEPT  NAME        SALARY
FIN   DIAZ        1,800
     ** FIN total (001):     1,800
 
HR    CRUZ        3,000
HR    ALVAREZ     2,500
     ** HR  total (002):     5,500
 
IT    BAUTISTA    3,100
IT    ESTRADA     2,800
IT    FLORES      2,200
     ** IT  total (003):     8,100
 
     ** GRAND total (006):    15,400
```

The parts worth studying:

- `1000-INITIALIZE` does the priming read, then saves the first department in `WS-PREV-DEPT`. If the file is empty, nothing is saved and nothing is printed.
- `2000-PROCESS-RECORD` tests `EMP-DEPT NOT = WS-PREV-DEPT` first, then handles the detail line. The department break therefore happens *between* groups, as the algorithm says.
- `2500-DEPT-BREAK` prints the subtotal, adds the group's count and total into the grand totals, resets the group accumulators, and saves the new key, in that order.
- `3000-FINALIZE` performs the **same** break paragraph once more for the last group, and then prints the grand total. Reusing the paragraph guarantees that the last group is treated exactly like every other.
- The IF around the final break protects against an empty input file, so the program prints a message instead of a bogus total.
- Edited pictures (`ZZ,ZZ9`, `Z,ZZZ,ZZ9`) make the numbers readable. The accumulators themselves stay plain numeric items (Lesson 8).

### Multi-level control breaks

Real reports often group on several fields at once: regions contain departments, departments contain employees. The control fields form a **hierarchy**, from **major** (outermost) to **minor** (innermost):

```text
REGION   (major)     <- changes least often
  DEPT   (minor)     <- changes more often
    employee detail records
```

The input must be sorted on all the control fields, major first (`ON ASCENDING KEY REGION DEPT`). The rules for a multi-level break are:

1. **A change in a higher level forces a break in every lower level.** If the region changes, the department must end too, even when the next record happens to carry the same department code. In the example below, department `IT` exists in both regions. When the region changes from `NA` to `SO`, `IT` is not "the same department"; it is the start of a new group.
2. **Breaks are processed from the lowest level upward.** Print the department total first, then the region total.
3. **Each level's total is added to the level above at its own break** (a *rolling* total). The department total flows into the region total, and the region total flows into the grand total. Detail records are added only to the lowest-level accumulator.
4. **Test the highest level first.** If the major key changed, perform both breaks. Only if it did not, test the minor key.

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. CBREAK2.

ENVIRONMENT DIVISION.
INPUT-OUTPUT SECTION.
FILE-CONTROL.
    SELECT EMP-FILE ASSIGN TO "emp-by-region.dat"
        ORGANIZATION IS LINE SEQUENTIAL.

DATA DIVISION.
FILE SECTION.
FD  EMP-FILE.
01  EMP-RECORD.
    05  EMP-REGION    PIC X(2).
    05  EMP-DEPT      PIC X(3).
    05  EMP-NAME      PIC X(8).
    05  EMP-SALARY    PIC 9(5).

WORKING-STORAGE SECTION.
01  WS-EOF            PIC X VALUE "N".
    88  END-OF-FILE         VALUE "Y".
01  WS-PREV-REGION    PIC X(2) VALUE SPACES.
01  WS-PREV-DEPT      PIC X(3) VALUE SPACES.
01  WS-DEPT-TOTAL     PIC 9(7) VALUE 0.
01  WS-REGION-TOTAL   PIC 9(8) VALUE 0.
01  WS-GRAND-TOTAL    PIC 9(9) VALUE 0.
01  WS-SAL-EDIT       PIC ZZ,ZZ9.
01  WS-TOT-EDIT       PIC ZZ,ZZZ,ZZ9.

PROCEDURE DIVISION.
0000-MAIN.
    PERFORM 0500-MAKE-TEST-DATA
    PERFORM 1000-INITIALIZE
    PERFORM 2000-PROCESS-RECORD UNTIL END-OF-FILE
    PERFORM 3000-FINALIZE
    STOP RUN.

0500-MAKE-TEST-DATA.
    OPEN OUTPUT EMP-FILE
    MOVE "NAHR CRUZ    03000" TO EMP-RECORD
    WRITE EMP-RECORD
    MOVE "NAHR ALVAREZ 02500" TO EMP-RECORD
    WRITE EMP-RECORD
    MOVE "NAIT BAUTIST 03100" TO EMP-RECORD
    WRITE EMP-RECORD
    MOVE "SOFINDIAZ    01800" TO EMP-RECORD
    WRITE EMP-RECORD
    MOVE "SOIT ESTRADA 02800" TO EMP-RECORD
    WRITE EMP-RECORD
    MOVE "SOIT FLORES  02200" TO EMP-RECORD
    WRITE EMP-RECORD
    CLOSE EMP-FILE.

1000-INITIALIZE.
    OPEN INPUT EMP-FILE
    PERFORM 2900-READ-NEXT
    IF NOT END-OF-FILE
        MOVE EMP-REGION TO WS-PREV-REGION
        MOVE EMP-DEPT   TO WS-PREV-DEPT
        DISPLAY "REGION " WS-PREV-REGION
    END-IF.

2000-PROCESS-RECORD.
    EVALUATE TRUE
        WHEN EMP-REGION NOT = WS-PREV-REGION
            PERFORM 2200-DEPT-BREAK
            PERFORM 2300-REGION-BREAK
        WHEN EMP-DEPT NOT = WS-PREV-DEPT
            PERFORM 2200-DEPT-BREAK
    END-EVALUATE
    MOVE EMP-SALARY TO WS-SAL-EDIT
    DISPLAY "    " EMP-DEPT " " EMP-NAME " " WS-SAL-EDIT
    ADD EMP-SALARY TO WS-DEPT-TOTAL
    PERFORM 2900-READ-NEXT.

2200-DEPT-BREAK.
    MOVE WS-DEPT-TOTAL TO WS-TOT-EDIT
    DISPLAY "  dept " WS-PREV-DEPT " total:  " WS-TOT-EDIT
    ADD WS-DEPT-TOTAL TO WS-REGION-TOTAL
    MOVE 0 TO WS-DEPT-TOTAL
    MOVE EMP-DEPT TO WS-PREV-DEPT.

2300-REGION-BREAK.
    MOVE WS-REGION-TOTAL TO WS-TOT-EDIT
    DISPLAY "region " WS-PREV-REGION " total: " WS-TOT-EDIT
    DISPLAY " "
    ADD WS-REGION-TOTAL TO WS-GRAND-TOTAL
    MOVE 0 TO WS-REGION-TOTAL
    MOVE EMP-REGION TO WS-PREV-REGION
    DISPLAY "REGION " WS-PREV-REGION.

2900-READ-NEXT.
    READ EMP-FILE
        AT END SET END-OF-FILE TO TRUE
    END-READ.

3000-FINALIZE.
    PERFORM 2200-DEPT-BREAK
    MOVE WS-REGION-TOTAL TO WS-TOT-EDIT
    DISPLAY "region " WS-PREV-REGION " total: " WS-TOT-EDIT
    ADD WS-REGION-TOTAL TO WS-GRAND-TOTAL
    MOVE WS-GRAND-TOTAL TO WS-TOT-EDIT
    DISPLAY "GRAND TOTAL:      " WS-TOT-EDIT
    CLOSE EMP-FILE.
```

Output:

```text
REGION NA
    HR  CRUZ      3,000
    HR  ALVAREZ   2,500
  dept HR  total:       5,500
    IT  BAUTIST   3,100
  dept IT  total:       3,100
region NA total:      8,600
 
REGION SO
    FIN DIAZ      1,800
  dept FIN total:       1,800
    IT  ESTRADA   2,800
    IT  FLORES    2,200
  dept IT  total:       5,000
region SO total:      6,800
GRAND TOTAL:          15,400
```

How the logic maps onto the rules:

- `EVALUATE TRUE` checks the **region first**. A new region performs `2200-DEPT-BREAK` and then `2300-REGION-BREAK`, lowest level first. Only if the region is unchanged does the second `WHEN` test the department.
- `2200-DEPT-BREAK` adds the department total to `WS-REGION-TOTAL`. `2300-REGION-BREAK` adds the region total to `WS-GRAND-TOTAL`. Detail lines add only to `WS-DEPT-TOTAL`.
- `3000-FINALIZE` closes out the last department, prints the last region total, and prints the grand total, which is the final break for every level.

### Variations

| Need | How |
|---|---|
| A count as well as a total | One more accumulator per level, reset at each break |
| Percent of the group or of the grand total | Compute it in the break paragraph with `COMPUTE ... ROUNDED` (Lesson 9). The grand total is only known at the end, so grand-total percentages need a second pass or a stored table |
| A heading for each new group | Print it at the end of the break paragraph, once the new key is saved (as `REGION NA` / `REGION SO` above), and check the page counter (Lesson 16) |
| Three or more levels | Add one `WHEN` per level, highest first, and one break paragraph per level. The pattern does not change |
| Page break on a major key | Perform the heading paragraph from the major-level break so each region starts on a new page |

---

## 18.3 Master-File Update (Match/Merge)

### The idea

A **master file** holds the permanent records of a system, such as customer accounts, in key order. A **transaction file** holds the day's changes: new accounts to **add**, balances to **change**, accounts to **delete**. The nightly job applies the transactions and produces a **new master file**:

```text
old master  ----+
                 +---> [ update program ] ---> new master
transactions ---+                        \--> reject / error report
 (sorted by key)
```

The old master is never modified. If the run fails, you rerun it from the same two input files, and the old master remains as a backup. This is the oldest and most reliable batch update design, and the **sequential match/merge** at its heart is the basis of many other programs (Lesson 15 shows the alternative of updating an indexed file in place).

### Requirements

- Both files are **sorted in ascending order on the same key** (the account number), and the master has **one record per key**. Use `SORT` on the transaction file first (Lesson 17). If transactions for the same key can depend on each other (an add followed by a change), sort with `WITH DUPLICATES IN ORDER` so they stay in the order they were entered.
- The key type and length are the same in both files, and the key is compared as one field.
- Several transactions can exist for one master record, and the program must handle all of them before moving on.

### The matching logic

At every step the program looks at the **current key of each file** and compares them. There are only three cases:

| Comparison | Meaning | Action |
|---|---|---|
| master key `<` transaction key | This master record has no (more) transactions | Write it to the new master unchanged, read the next master |
| master key `=` transaction key | The transaction applies to this master record | Apply it (change, delete, or reject an add), read the next transaction. Keep the master record in memory |
| master key `>` transaction key | There is no master record for this transaction | If it is an **add**, create the new record. Otherwise, reject it. Read the next transaction |

### `HIGH-VALUES` as the end-of-file marker

When one file runs out, the other must still be processed to its end. The trick is that, at end of file, the program sets that file's key to `HIGH-VALUES` (Lesson 5), which compares greater than any real key:

- If the master ends first, its key is `HIGH-VALUES`, so every remaining transaction falls into the "master key `>` transaction key" case (adds are created; the rest are rejected).
- If the transactions end first, their key is `HIGH-VALUES`, so every remaining master record falls into "master key `<` transaction key" and is copied across.
- The loop stops only when **both** keys are `HIGH-VALUES`:

```cobol
PERFORM 2000-MATCH
    UNTIL WS-M-KEY = HIGH-VALUES AND WS-T-KEY = HIGH-VALUES
```

This removes all special end-of-file handling from the main logic. It is why the program keeps separate key fields (`WS-M-KEY`, `WS-T-KEY`) and sets only those to `HIGH-VALUES`, never the data fields of the records.

### The complete program

The test data is built inside the program, as in earlier lessons. The old master has four accounts (1001, 1002, 1003, 1005). The transactions, in key order, are: two changes to 1001, a delete of 1003, an add of 1004, an add of 1005 (which already exists), and a change to 1006 (which does not exist). In the transaction file, `A` means add, `C` means change (add the amount to the balance), and `D` means delete.

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. MASTUPD.

ENVIRONMENT DIVISION.
INPUT-OUTPUT SECTION.
FILE-CONTROL.
    SELECT OLD-MASTER ASSIGN TO "master-old.dat"
        ORGANIZATION IS LINE SEQUENTIAL.
    SELECT TRANS-FILE ASSIGN TO "trans.dat"
        ORGANIZATION IS LINE SEQUENTIAL.
    SELECT NEW-MASTER ASSIGN TO "master-new.dat"
        ORGANIZATION IS LINE SEQUENTIAL.

DATA DIVISION.
FILE SECTION.
FD  OLD-MASTER.
01  OLD-REC           PIC X(18).

FD  TRANS-FILE.
01  TRANS-REC.
    05  TRAN-TYPE     PIC X.
    05  TRAN-ACCT     PIC X(4).
    05  TRAN-NAME     PIC X(8).
    05  TRAN-AMOUNT   PIC 9(5).

FD  NEW-MASTER.
01  NEW-REC           PIC X(18).

WORKING-STORAGE SECTION.
01  WS-MASTER.
    05  M-ACCT        PIC X(4).
    05  M-NAME        PIC X(8).
    05  M-BALANCE     PIC 9(6).
01  WS-NEW-ACCOUNT.
    05  N-ACCT        PIC X(4).
    05  N-NAME        PIC X(8).
    05  N-BALANCE     PIC 9(6).
01  WS-M-DELETED-FLAG PIC X VALUE "N".
    88  M-DELETED           VALUE "Y".
    88  M-KEPT              VALUE "N".
01  WS-M-KEY          PIC X(4).
01  WS-T-KEY          PIC X(4).
01  WS-LIST-EOF       PIC X VALUE "N".
    88  END-OF-LIST         VALUE "Y".
01  WS-COUNTS.
    05  WS-CHANGED    PIC 99 VALUE 0.
    05  WS-ADDED      PIC 99 VALUE 0.
    05  WS-DELETED    PIC 99 VALUE 0.
    05  WS-REJECTED   PIC 99 VALUE 0.

PROCEDURE DIVISION.
0000-MAIN.
    PERFORM 0500-MAKE-TEST-DATA
    PERFORM 1000-INITIALIZE
    PERFORM 2000-MATCH
        UNTIL WS-M-KEY = HIGH-VALUES AND WS-T-KEY = HIGH-VALUES
    PERFORM 3000-FINALIZE
    STOP RUN.

0500-MAKE-TEST-DATA.
    OPEN OUTPUT OLD-MASTER
    MOVE "1001ALVAREZ 005000" TO OLD-REC
    WRITE OLD-REC
    MOVE "1002BAUTISTA012000" TO OLD-REC
    WRITE OLD-REC
    MOVE "1003CRUZ    000750" TO OLD-REC
    WRITE OLD-REC
    MOVE "1005ESTRADA 002500" TO OLD-REC
    WRITE OLD-REC
    CLOSE OLD-MASTER
    OPEN OUTPUT TRANS-FILE
    MOVE "C1001        00500" TO TRANS-REC
    WRITE TRANS-REC
    MOVE "C1001        00250" TO TRANS-REC
    WRITE TRANS-REC
    MOVE "D1003        00000" TO TRANS-REC
    WRITE TRANS-REC
    MOVE "A1004DIAZ    01000" TO TRANS-REC
    WRITE TRANS-REC
    MOVE "A1005ESTRADA 00000" TO TRANS-REC
    WRITE TRANS-REC
    MOVE "C1006        00100" TO TRANS-REC
    WRITE TRANS-REC
    CLOSE TRANS-FILE.

1000-INITIALIZE.
    OPEN INPUT OLD-MASTER TRANS-FILE
         OUTPUT NEW-MASTER
    PERFORM 2800-READ-MASTER
    PERFORM 2900-READ-TRANS.

2000-MATCH.
    EVALUATE TRUE
        WHEN WS-M-KEY < WS-T-KEY
            PERFORM 2100-COPY-MASTER
        WHEN WS-M-KEY = WS-T-KEY
            PERFORM 2200-APPLY-TRANS
        WHEN OTHER
            PERFORM 2300-UNMATCHED-TRANS
    END-EVALUATE.

2100-COPY-MASTER.
    IF M-KEPT
        WRITE NEW-REC FROM WS-MASTER
    END-IF
    PERFORM 2800-READ-MASTER.

2200-APPLY-TRANS.
    EVALUATE TRAN-TYPE
        WHEN "C"
            ADD TRAN-AMOUNT TO M-BALANCE
                ON SIZE ERROR
                    DISPLAY "REJECT " TRAN-ACCT " balance overflow"
                    ADD 1 TO WS-REJECTED END-ADD
                NOT ON SIZE ERROR
                    ADD 1 TO WS-CHANGED END-ADD
            END-ADD
        WHEN "D"
            SET M-DELETED TO TRUE
            ADD 1 TO WS-DELETED
        WHEN "A"
            DISPLAY "REJECT " TRAN-ACCT " already exists"
            ADD 1 TO WS-REJECTED
        WHEN OTHER
            DISPLAY "REJECT " TRAN-ACCT " bad type " TRAN-TYPE
            ADD 1 TO WS-REJECTED
    END-EVALUATE
    PERFORM 2900-READ-TRANS.

2300-UNMATCHED-TRANS.
    IF TRAN-TYPE = "A"
        MOVE TRAN-ACCT   TO N-ACCT
        MOVE TRAN-NAME   TO N-NAME
        MOVE TRAN-AMOUNT TO N-BALANCE
        WRITE NEW-REC FROM WS-NEW-ACCOUNT
        ADD 1 TO WS-ADDED
    ELSE
        DISPLAY "REJECT " TRAN-ACCT " no such account"
        ADD 1 TO WS-REJECTED
    END-IF
    PERFORM 2900-READ-TRANS.

2800-READ-MASTER.
    READ OLD-MASTER INTO WS-MASTER
        AT END
            MOVE HIGH-VALUES TO WS-M-KEY
        NOT AT END
            MOVE M-ACCT TO WS-M-KEY
            SET M-KEPT TO TRUE
    END-READ.

2900-READ-TRANS.
    READ TRANS-FILE
        AT END
            MOVE HIGH-VALUES TO WS-T-KEY
        NOT AT END
            MOVE TRAN-ACCT TO WS-T-KEY
    END-READ.

3000-FINALIZE.
    CLOSE OLD-MASTER TRANS-FILE NEW-MASTER
    DISPLAY "Changed:  " WS-CHANGED
    DISPLAY "Added:    " WS-ADDED
    DISPLAY "Deleted:  " WS-DELETED
    DISPLAY "Rejected: " WS-REJECTED
    DISPLAY "--- new master ---"
    PERFORM 3100-LIST-NEW-MASTER.

3100-LIST-NEW-MASTER.
    OPEN INPUT NEW-MASTER
    PERFORM UNTIL END-OF-LIST
        READ NEW-MASTER
            AT END
                SET END-OF-LIST TO TRUE
            NOT AT END
                DISPLAY NEW-REC
        END-READ
    END-PERFORM
    CLOSE NEW-MASTER.
```

Output:

```text
REJECT 1005 already exists
REJECT 1006 no such account
Changed:  02
Added:    01
Deleted:  01
Rejected: 02
--- new master ---
1001ALVAREZ 005750
1002BAUTISTA012000
1004DIAZ    001000
1005ESTRADA 002500
```

### Reading the result

| Account | What happened | In the new master |
|---|---|---|
| 1001 | Two changes (`+500`, then `+250`) applied to the same master record | Balance `005750` |
| 1002 | No transactions: copied unchanged | Present |
| 1003 | Deleted | Absent |
| 1004 | Added (no master record existed) | Present, balance `001000` |
| 1005 | An add was rejected because the account already exists | Unchanged |
| 1006 | A change was rejected because there is no such account | Not created |

### Design points to copy

- **The master record is held in `WS-MASTER` while its transactions are applied.** That is how several transactions can update one record. It is written only when the master key becomes smaller than the transaction key (`2100-COPY-MASTER`), never at the moment a transaction is applied.
- **Deletion is a flag**, not a skipped read. `M-DELETED` tells `2100-COPY-MASTER` not to write the record. The flag is reset to `M-KEPT` every time a new master record is read.
- **Adds use their own work area** (`WS-NEW-ACCOUNT`). The program must not build the new record in `WS-MASTER`, because at that moment `WS-MASTER` still holds the next real master record (here, 1005), which would be overwritten and lost.
- **Every rejected transaction is reported**, with a reason, and counted. Never drop a bad transaction silently.
- **Control counts** (changed, added, deleted, rejected) let operators check the run against the number of transactions received. A common rule is *transactions read = changed + added + deleted + rejected*.
- **`ON SIZE ERROR` protects the balance** (Lesson 9). Notice that the statements inside the `ON SIZE ERROR` branch end with `END-ADD`. Without it, the second `ADD` swallows the `NOT ON SIZE ERROR` phrase, which then belongs to the *inner* statement, and the "changed" counter stays at zero without any compiler error. When an arithmetic verb with `ON SIZE ERROR` contains other statements of the same verb, give each its own scope terminator.
- **A limitation of this version:** an add is written straight to the new master, so a second transaction for the *same new* account (for example, "add 1004" followed by "change 1004") would be rejected as "no such account". A production program holds the new record in memory in the same way as an existing one, or applies the change before writing.

### Making it production-ready

| Addition | Why |
|---|---|
| Sequence checks | Compare each record's key with the previous one and stop the job if a file is out of order. Unsorted input otherwise produces silent garbage |
| `FILE STATUS` after every `OPEN`, `READ`, and `WRITE` | Detect missing files and full disks (Lessons 14 and 21) |
| A separate reject file | Lets the business correct and resubmit the bad transactions |
| A reconciliation report | Old master count + adds - deletes = new master count |
| Non-zero `RETURN-CODE` on errors | The scheduler (or JCL condition codes) can then stop dependent jobs (Lesson 31) |

The same three-way comparison extends to more than two files (for example, a master and several transaction files, or two masters being combined). The structure stays the same: find the lowest current key, process everything that carries it, and advance only the files that supplied it.

---

[Previous](./[17]-Sorting-and-Merging.md) | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next](./[19]-Subprograms.md)
