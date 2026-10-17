[⬅ Back to README](../../../README.md)

<div align="center">
  <img src="https://api.iconify.design/vscode-icons/file-type-cobol.svg" width="50"/><br>
  <h1 style="margin-top: 0;">COBOL</h1>
</div>

Welcome! This is a self-paced course for learning COBOL (COmmon Business-Oriented Language), the readable, record-oriented language that still runs a large share of the world's banking, insurance, payroll, government, and airline systems.

Download: [https://gnucobol.sourceforge.io/](https://gnucobol.sourceforge.io/)

> This course uses **GnuCOBOL** so you can practice on a normal computer, with notes on how things differ on IBM mainframes (z/OS, CICS, JCL).

---

## What is COBOL?

COBOL was designed in 1959 for business data processing. Its English-like syntax is meant to be readable by non-programmers, and it excels at exact decimal arithmetic and at processing large volumes of fixed-format records. Billions of lines of it are still in production, and developers who understand it are in steady demand.

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. HELLO.
PROCEDURE DIVISION.
    DISPLAY "Hello, World!".
    STOP RUN.
```

COBOL lets you:
- Write readable, self-documenting business logic
- Do exact decimal arithmetic without floating-point rounding surprises
- Describe data precisely with `PICTURE` clauses and record layouts
- Process sequential, indexed, and relative files
- Generate formatted reports and handle batch jobs at huge scale
- Sort, merge, and transform large data sets
- Work with databases through embedded SQL
- Build transaction systems on mainframes (CICS, IMS)
- Maintain and modernize legacy systems
- Interoperate with C, Java, and web services

**Prerequisites:** none. If you have programmed before, you will notice that COBOL is verbose by design. Chapter 4 explains the program layout rules, which are the main surprise for newcomers.

## Table of Contents

**Getting Started**  

1. **[Installing a COBOL Compiler (GnuCOBOL, IBM Enterprise COBOL)](./[1]-Installation-and-Setup.md)**  
    1.1 What Is a COBOL Compiler?  
    1.2 Installing GnuCOBOL (Linux / macOS / Windows)  
    1.3 Using Online Compilers and Docker  
    1.4 Mainframe Access Options (IBM Z Xplore)  
    1.5 Verifying Your Installation  
2. **[Compiling & Running Your First Program](./[2]-Compiling-and-Running.md)**  
    2.1 From Source Code to Executable  
    2.2 Compiling with `cobc`  
    2.3 Free-Format vs Fixed-Format Source  
    2.4 Running and Reading Compiler Errors  
    2.5 Common Compiler Flags  
3. **[Your Development Environment (editors, debugging, VS Code extensions)](./[3]-Development-Environment.md)**  
    3.1 Choosing an Editor or IDE  
    3.2 Syntax Highlighting and Extensions  
    3.3 Debugging with GnuCOBOL  
    3.4 A Recommended Project Layout  

**Core Syntax**  

4. **[Program Structure (the four divisions, columns, and layout rules)](./[4]-Program-Structure.md)**  
    4.1 IDENTIFICATION DIVISION  
    4.2 ENVIRONMENT DIVISION  
    4.3 DATA DIVISION  
    4.4 PROCEDURE DIVISION  
    4.5 Columns, Areas A and B, and Comments  
5. **[Variables & Data Types (`PICTURE` clauses, levels, `USAGE`)](./[5]-Variables-and-Data-Types.md)**  
    5.1 What Is a Data Item?  
    5.2 Numeric, Alphabetic, and Alphanumeric `PIC` Clauses  
    5.3 Level Numbers (01, 05, 77, 88)  
    5.4 `VALUE`, Figurative Constants, and Constants  
    5.5 Naming Conventions  
6. **[Group Items, Records & Tables (`OCCURS`)](./[6]-Group-Items-and-Tables.md)**  
    6.1 Group vs Elementary Items  
    6.2 Records and Nested Structures  
    6.3 `OCCURS` and Single-Dimension Tables  
    6.4 Multi-Dimensional Tables, `INDEXED BY`, and `SEARCH`  
    6.5 `REDEFINES` and `RENAMES`  
7. **[Input & Output with `DISPLAY` and `ACCEPT`](./[7]-Display-and-Accept.md)**  
    7.1 `DISPLAY`  
    7.2 `ACCEPT` from the User, Date, and Time  
    7.3 Screen Section Basics  
8. **[Moving & Editing Data (`MOVE`, edited pictures)](./[8]-MOVE-and-Editing.md)**  
    8.1 The `MOVE` Statement  
    8.2 Truncation and Padding Rules  
    8.3 `CORRESPONDING`  
    8.4 Edited Pictures (`$`, `,`, `.`, `Z`, `*`)  
9. **[Arithmetic (`ADD`, `COMPUTE`, `ROUNDED`, `ON SIZE ERROR`)](./[9]-Arithmetic.md)**  
    9.1 `ADD`, `SUBTRACT`, `MULTIPLY`, `DIVIDE`  
    9.2 `COMPUTE` and Expressions  
    9.3 `ROUNDED` and `ON SIZE ERROR`  
    9.4 Decimal Precision and `COMP-3`  
    9.5 Intrinsic Functions  
10. **[Conditionals (`IF`, `EVALUATE`, condition names)](./[10]-Conditionals.md)**  
    10.1 `IF`, `ELSE`, and `END-IF`  
    10.2 Relational, Class, and Sign Conditions  
    10.3 Level-88 Condition Names  
    10.4 `EVALUATE` (the COBOL `switch`)  
11. **[Loops (`PERFORM`, `UNTIL`, `VARYING`)](./[11]-Loops.md)**  
    11.1 Inline `PERFORM`  
    11.2 `PERFORM UNTIL`  
    11.3 `PERFORM VARYING`  
    11.4 `PERFORM n TIMES`  
    11.5 Nested Loops  
12. **[Paragraphs, Sections & Structured Programming](./[12]-Paragraphs-and-Sections.md)**  
    12.1 Paragraphs and `PERFORM`  
    12.2 Sections and `PERFORM THRU`  
    12.3 `GO TO` and Why to Avoid It  
    12.4 Writing Clean, Top-Down Programs  
13. **[String Handling (`STRING`, `UNSTRING`, `INSPECT`)](./[13]-String-Handling.md)**  
    13.1 Reference Modification  
    13.2 `STRING` and `UNSTRING`  
    13.3 `INSPECT` (Tallying and Replacing)  
    13.4 Intrinsic String Functions  

**Files & Data Processing**  

14. **[Sequential File Processing](./[14]-Sequential-Files.md)**  
    14.1 `SELECT`, `ASSIGN`, and `FD`  
    14.2 `OPEN`, `READ`, `WRITE`, `CLOSE`  
    14.3 `AT END` and `FILE STATUS`  
    14.4 The Classic Read-Process-Write Loop  
15. **[Indexed & Relative Files](./[15]-Indexed-and-Relative-Files.md)**  
    15.1 Keys and Access Modes  
    15.2 `READ`, `REWRITE`, `DELETE`, `START`  
    15.3 Random vs Sequential vs Dynamic Access  
16. **[Report Writing & Formatting Output](./[16]-Report-Writing.md)**  
    16.1 Headings, Detail Lines, and Totals  
    16.2 Page Breaks and Line Counters  
    16.3 Report Writer Basics  
17. **[Sorting & Merging (`SORT`, `MERGE`)](./[17]-Sorting-and-Merging.md)**  
    17.1 `SORT` with `USING` and `GIVING`  
    17.2 Input and Output Procedures  
    17.3 `MERGE`  
18. **[Control Breaks & Batch Processing Patterns](./[18]-Control-Breaks-and-Batch.md)**  
    18.1 What Is a Control Break?  
    18.2 Single and Multi-Level Totals  
    18.3 Master-File Update (Match/Merge)  

**Modular Programming**  

19. **[Subprograms (`CALL`, `LINKAGE SECTION`, `USING`)](./[19]-Subprograms.md)**  
    19.1 `CALL` and `CALL ... USING`  
    19.2 `BY REFERENCE`, `BY CONTENT`, `BY VALUE`  
    19.3 The `LINKAGE SECTION`  
    19.4 Return Codes and `GOBACK`  
20. **[Copybooks (`COPY`, `REPLACE`)](./[20]-Copybooks.md)**  
    20.1 Sharing Record Layouts  
    20.2 `COPY ... REPLACING`  
    20.3 Organizing a Copybook Library  
21. **[Error Handling & Debugging](./[21]-Error-Handling-and-Debugging.md)**  
    21.1 File Status Codes  
    21.2 `ON EXCEPTION` and `INVALID KEY`  
    21.3 Tracing with `DISPLAY` and `READY TRACE`  
    21.4 Common Runtime Errors  

**Data Representation Deep Dive**  

22. **[Numeric Storage Formats (`COMP`, `COMP-3`, `COMP-5`)](./[22]-Numeric-Storage-Formats.md)**  
    22.1 Display vs Binary vs Packed Decimal  
    22.2 Sign Handling  
    22.3 Performance and Storage Trade-offs  
23. **[Dates, Times & Intrinsic Functions](./[23]-Dates-and-Functions.md)**  
    23.1 `CURRENT-DATE` and `INTEGER-OF-DATE`  
    23.2 Date Arithmetic and Y2K Lessons  
    23.3 Math, String, and Financial Functions  
24. **[Character Sets: ASCII vs EBCDIC](./[24]-ASCII-vs-EBCDIC.md)**  
    24.1 Why EBCDIC Exists  
    24.2 Collating Sequences  
    24.3 Converting Between Encodings  

**Modern COBOL**  

25. **[Object-Oriented COBOL](./[25]-Object-Oriented-COBOL.md)**  
26. **[Working with JSON & XML](./[26]-JSON-and-XML.md)**  
27. **[Embedded SQL (DB2, PostgreSQL)](./[27]-Embedded-SQL.md)**  
28. **[Calling C & Interoperating with Other Languages](./[28]-Interoperability.md)**  
29. **[Exposing COBOL as Web Services & APIs](./[29]-Web-Services.md)**  

**The Mainframe World**  

30. **[Mainframe Basics (z/OS, TSO/ISPF, datasets)](./[30]-Mainframe-Basics.md)**  
    30.1 What Is a Mainframe?  
    30.2 Datasets, Members, and Partitioned Libraries  
    30.3 Using ISPF  
31. **[JCL: Job Control Language](./[31]-JCL.md)**  
    31.1 `JOB`, `EXEC`, and `DD` Statements  
    31.2 Compiling and Running COBOL via JCL  
    31.3 Utilities (`IEBGENER`, `SORT`, `IDCAMS`)  
32. **[CICS & Online Transaction Processing](./[32]-CICS.md)**  
33. **[VSAM Files & Batch Scheduling](./[33]-VSAM-and-Scheduling.md)**  

**Maintaining Legacy Code**  

34. **[Reading & Understanding Legacy Programs](./[34]-Reading-Legacy-Code.md)**  
35. **[Refactoring & Modernizing COBOL Applications](./[35]-Refactoring-and-Modernizing.md)**  
36. **[Testing COBOL (unit tests, `cobol-check`)](./[36]-Testing.md)**  

**Projects & Best Practices**  

37. **[Project: Payroll Calculator](./[37]-Project-Payroll.md)**  
38. **[Project: Bank Account Ledger with Indexed Files](./[38]-Project-Bank-Ledger.md)**  
39. **[Project: Inventory Report Generator](./[39]-Project-Inventory-Report.md)**  
40. **[COBOL Style Guides & Idiomatic COBOL](./[40]-Best-Practices-and-Style.md)**