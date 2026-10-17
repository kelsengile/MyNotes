[Previous]([15]-Indexed-and-Relative-Files.md) | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([17]-Sorting-and-Merging.md)

*Files & Data Processing*

# Lesson 16 - Report Writing & Formatting Output

Producing printed or text reports is one of COBOL's original jobs. A report is built from a few repeating ingredients: headings, detail lines, and totals. This lesson shows how to build them by hand with edited pictures, how to handle page breaks, and what the built-in Report Writer feature offers.

---

## 16.1 Headings, Detail Lines, and Totals

### Print lines are just records

The standard technique is to declare each kind of line as its own **group item** in `WORKING-STORAGE`, with `FILLER` for fixed text and spacing, and edited fields for the values. The program fills in the variable parts and writes the whole group to the report file:

```cobol
01  HEADING-2.
    05  FILLER           PIC X(14) VALUE "PRODUCT".
    05  FILLER           PIC X(8)  VALUE SPACES.
    05  FILLER           PIC X(8)  VALUE "QUANTITY".
01  DETAIL-LINE.
    05  DT-NAME          PIC X(14).
    05  FILLER           PIC X(10) VALUE SPACES.
    05  DT-QTY           PIC ZZ,ZZ9.
```

```cobol
MOVE WS-P-NAME(WS-I) TO DT-NAME
MOVE WS-P-QTY(WS-I)  TO DT-QTY
WRITE REPORT-LINE FROM DETAIL-LINE AFTER ADVANCING 1 LINE
```

The layout of a line is visible in the data description: the sizes of the fields and fillers add up to the line width, so you can count columns directly. Use **edited pictures** (Lesson 8) for every number, so values appear with commas, decimal points, and suppressed leading zeros.

### The report file

A report is written to an ordinary file, usually `LINE SEQUENTIAL` on a PC (and a `SYSOUT` print dataset on the mainframe). Declare a record wide enough for your longest line:

```cobol
FD  REPORT-FILE.
01  REPORT-LINE          PIC X(40).
```

### `WRITE ... ADVANCING`: controlling vertical spacing

| Form | Effect |
|---|---|
| `WRITE rec AFTER ADVANCING 1 LINE` | Move down one line, then print (single spacing) |
| `WRITE rec AFTER ADVANCING 2 LINES` | Skip a line first (double spacing) |
| `WRITE rec AFTER ADVANCING PAGE` | Start a new page (form feed), then print |
| `WRITE rec BEFORE ADVANCING 1 LINE` | Print, then move down |

Use one style, `AFTER` or `BEFORE`, consistently throughout a program, because mixing them in the same file causes spacing surprises. A `PAGE` advance is how you start every page after the first with a heading.

### Totals

Accumulate totals in ordinary numeric items while processing detail lines. Print them at the end with an edited picture:

```cobol
ADD WS-P-QTY(WS-I) TO WS-TOTAL-QTY
...
MOVE WS-TOTAL-QTY TO TL-QTY
WRITE REPORT-LINE FROM TOTAL-LINE AFTER ADVANCING 1 LINE
```

Use a line of dashes or equals signs above the total to show it is a sum.

---

## 16.2 Page Breaks and Line Counters

Printed reports have a fixed number of lines per page. The program keeps a **line counter**, and before printing each detail line it checks whether the page is full. If so, it starts a new page by printing the headings again, with an updated page number.

The pattern is:

1. Initialize the line counter to a value that forces headings on the first detail line (for example `99`).
2. Before every detail line, test `IF WS-LINE-COUNT >= WS-LINES-PER-PAGE`, and if so perform the heading paragraph.
3. The heading paragraph adds 1 to the page number, writes the headings, and **resets** the line counter to the number of lines the headings used.
4. After each line written, add to the line counter.

A complete program (the page size is set artificially small, 5 lines, so that you can see page breaks with only seven rows):

```cobol
''' + rep1 + '''```

The report file `sales.rpt` contains the following. The symbol `<FF>` marks the form-feed character that the `AFTER ADVANCING PAGE` writes between pages (it is invisible in a text editor, and a printer treats it as "eject the page"):

```text
<FF>STOCK REPORT              PAGE  1
PRODUCT               QUANTITY
------------------------------
Pencils                    120
Notebooks                   45<FF>STOCK REPORT              PAGE  2
PRODUCT               QUANTITY
------------------------------
Erasers                    300
Rulers                      80<FF>STOCK REPORT              PAGE  3
PRODUCT               QUANTITY
------------------------------
Markers                     60
Staplers                    15<FF>STOCK REPORT              PAGE  4
PRODUCT               QUANTITY
------------------------------
Folders                    200
------------------------------
TOTAL                     820
```

Points to notice:

- The first detail line triggers the headings because `WS-LINE-COUNT` starts at 99. No special "first page" code is needed.
- `WS-LINE-COUNT` is reset to 3, the number of heading lines, so each page holds three heading lines plus two detail lines (5 in all).
- Real reports use a page size such as 55 or 60 lines. Change `WS-LINES-PER-PAGE` and nothing else.
- When you print a total, you may also need to check for a page break, so the total does not land alone on a new page.
- `HD-PAGE` is an edited field (`PIC Z9`), so page numbers print as ` 1`, ` 2`, and so on without a leading zero.
- Line widths here are small to keep the example short. Standard widths are 80 columns for screen output and 132 for line printers.

> **Mainframe note.** On z/OS the report file is usually a `SYSOUT` print dataset, and many shops still use a one-character *carriage control* in the first column of each print line (a blank for single space, `0` for double space, `1` for a new page). `WRITE ... ADVANCING` makes the compiler generate those characters for you.

---

## 16.3 Report Writer Basics

The **Report Writer** is a COBOL feature that moves the report mechanics (page counting, headings, control breaks, and totals) out of your procedure code and into declarations. You describe what each kind of line looks like in a `REPORT SECTION`, and the runtime decides when to print it.

Use the keyword statements `INITIATE`, `GENERATE`, and `TERMINATE` to drive it:

| Statement | Purpose |
|---|---|
| `INITIATE report-name` | Starts the report: resets counters and sums |
| `GENERATE detail-name` | Processes one detail item: prints headings if needed, prints the line, adds up `SUM` fields |
| `TERMINATE report-name` | Finishes the report: prints the final totals and footings |

### The report definition

- The file's `FD` names the report with `REPORT IS report-name` (and has no record layout of its own).
- The `REPORT SECTION` has an `RD` entry per report that sets the page size and the control fields.
- Below the `RD`, **report groups** describe the lines. Each group has a `TYPE`:

| `TYPE` | Printed |
|---|---|
| `REPORT HEADING` / `REPORT FOOTING` | Once, at the very start / end of the report |
| `PAGE HEADING` / `PAGE FOOTING` | At the top / bottom of every page |
| `DETAIL` | Each time you `GENERATE` it |
| `CONTROL HEADING` / `CONTROL FOOTING` | When a control field changes (and `FINAL` at the end), for group totals |

- Entries inside a group use `LINE` and `COLUMN` to place items. Use `VALUE` for fixed text, `SOURCE` to print a data item, and `SUM` to accumulate a total automatically. `PAGE-COUNTER` and `LINE-COUNTER` are special registers that Report Writer maintains.
- `LINE PLUS 1` means "one line below the last line printed".

### The same stock report with Report Writer

```cobol
''' + rw2 + '''```

Output file `stock.rpt`:

```text
STOCK REPORT       PAGE  1
PRODUCT              QUANTITY
------------------------------
Pencils                   120
Notebooks                  45
Erasers                   300
------------------------------
TOTAL                    360


```

Compare this with the manual version: there is no line counter, no heading paragraph, and no total accumulator. `GENERATE ITEM-LINE` prints the heading when needed (the first time, and on any new page), prints the detail line, and adds `WS-QTY(WS-I)` to the `SUM` automatically. `TERMINATE` prints the `CONTROL FOOTING FINAL` line with the total. When the report ends, the last page is padded with blank lines up to the page limit (`PAGE LIMIT IS 10 LINES` here), which is why two blank lines appear at the end.

### Control breaks with Report Writer

Naming a field in `CONTROLS ARE` (for example `CONTROLS ARE FINAL, REGION`) makes `GENERATE` watch that field and print the `CONTROL HEADING` and `CONTROL FOOTING` groups for it whenever its value changes. Each footing can contain its own `SUM` totals, which roll up into the next level. Report Writer does this without any comparison code in your program, but the input must still be **sorted** by the control field (Lesson 17). Lesson 18 builds the same control-break logic by hand so that you understand what the feature automates.

### Should you use Report Writer?

| Consideration | Notes |
|---|---|
| **Availability** | Part of the COBOL standard and supported by GnuCOBOL, but **IBM Enterprise COBOL for z/OS does not support it**. Most mainframe shops therefore write reports by hand as in 16.1 and 16.2 |
| **Strength** | Very compact for multi-level totals and heading logic |
| **Weakness** | The declarations have a learning curve, and behavior details (padding, page-end handling) vary between compilers |
| **Recommendation** | Learn the manual technique first. It works everywhere, and it is the style you will meet in most legacy code |

---

[Previous]([15]-Indexed-and-Relative-Files.md) | [Table of Contents](./[0]-Introduction-to-COBOL.md) | [Next]([17]-Sorting-and-Merging.md)