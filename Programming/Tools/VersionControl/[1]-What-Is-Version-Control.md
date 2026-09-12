[Previous](./[0]-Introduction-to-VersionControl.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[2]-Core-Version-Control-Concepts.md)

*Foundations*

# Lesson 1 - What Is Version Control

## 1.1 Defining Version Control

Version control is a system for recording changes to a file or set of files over time, so that you can recall specific versions later. Instead of manually saving copies like `report_final.docx` and `report_final_v2_ACTUAL.docx`, a version control system keeps a complete, structured history of every change — what changed, when it changed, and who made the change — all in one place. This applies to any kind of file, but it's most heavily used for source code, where teams of developers are constantly editing the same set of files.

A folder full of manual copies might look like this:

```
report.docx
report_v2.docx
report_v2_edits.docx
report_final.docx
report_final_ACTUAL.docx
report_final_ACTUAL_useThisOne.docx
```

With version control, there's just one file, `report.docx`, and every one of those "versions" is instead a recorded snapshot in its history — inspectable, comparable, and restorable on demand, without a single ambiguous filename.

---

## 1.2 Why Version Control Matters

Without version control, collaborating on a shared codebase quickly becomes chaotic: changes get overwritten, nobody can tell why a piece of code looks the way it does, and reverting a bad change means manually undoing it by hand. Version control solves these problems by giving every change a permanent, inspectable record, letting multiple people work on the same project simultaneously without directly overwriting each other's work, and making it possible to jump back to any previous state of the project in seconds. It's also invaluable for solo projects, since it turns "I broke something and I'm not sure what" into a solvable problem rather than a disaster.

For example, if a bug is traced back to a specific change, a developer can ask exactly when and why that change was made:

```
$ git log --oneline -- src/payments.py
a1b2c3d Fix rounding error in tax calculation
7e8f9a0 Add support for discount codes
3c4d5e6 Initial payment processing module
```

That short list alone answers "when did this file last change, and why" in seconds — a question that could take hours to answer by comparing manually-saved copies.

---

## 1.3 Centralized vs Distributed Version Control

Version control systems fall into two broad categories. **Centralized** systems, like Subversion (SVN), keep the full project history on a single central server, and each user's computer holds only the specific version they're currently working on. **Distributed** systems, like Git and Mercurial, give every user a complete copy of the entire project history on their own machine, so most operations (viewing history, creating branches, committing changes) happen locally without needing network access, and any user's copy can act as a full backup of the project. Distributed systems have become the dominant approach for their speed, resilience, and flexibility, which is why this Topic focuses on Git.

| | Centralized (e.g. SVN) | Distributed (e.g. Git) |
|---|---|---|
| Full history lives on | One central server | Every clone |
| Works offline? | Mostly no | Yes, almost entirely |
| Single point of failure? | Yes (the server) | No — every clone is a full backup |
| Typical operations | Network round-trip required | Local and instant |

---

## 1.4 A Brief History Of Version Control

Early version control tools from the 1970s and 80s, like SCCS and RCS, tracked changes to individual files one at a time on a single machine. CVS, introduced in the late 1980s, extended this to track changes across an entire project and allowed multiple developers to work concurrently, though it remained centralized. Subversion improved on CVS's reliability and feature set through the 2000s. Git was created by Linus Torvalds in 2005 to manage the Linux kernel's source code, prioritizing speed, distributed workflows, and strong guarantees about data integrity — and its design, along with the rise of hosting platforms like GitHub, has made it the dominant version control system in use today.

A rough timeline:

```
1972 ── SCCS          single-file, single-machine tracking
1982 ── RCS           improved single-file tracking
1986 ── CVS           first to version whole projects, still centralized
2000 ── Subversion     more reliable, still centralized
2005 ── Git            distributed, built for the Linux kernel
2008 ── GitHub launches, accelerating Git's dominance
```

[Previous](./[0]-Introduction-to-VersionControl.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[2]-Core-Version-Control-Concepts.md)
