[Previous](./[0]-Introduction-to-VersionControl.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[2]-Core-Version-Control-Concepts.md)

*Foundations*

# Lesson 1 - What Is Version Control

## 1.1 Defining Version Control

Version control is a system for recording changes to a file or set of files over time, so that you can recall specific versions later. Instead of manually saving copies like `report_final.docx` and `report_final_v2_ACTUAL.docx`, a version control system keeps a complete, structured history of every change — what changed, when it changed, and who made the change — all in one place. This applies to any kind of file, but it's most heavily used for source code, where teams of developers are constantly editing the same set of files.

---

## 1.2 Why Version Control Matters

Without version control, collaborating on a shared codebase quickly becomes chaotic: changes get overwritten, nobody can tell why a piece of code looks the way it does, and reverting a bad change means manually undoing it by hand. Version control solves these problems by giving every change a permanent, inspectable record, letting multiple people work on the same project simultaneously without directly overwriting each other's work, and making it possible to jump back to any previous state of the project in seconds. It's also invaluable for solo projects, since it turns "I broke something and I'm not sure what" into a solvable problem rather than a disaster.

---

## 1.3 Centralized vs Distributed Version Control

Version control systems fall into two broad categories. **Centralized** systems, like Subversion (SVN), keep the full project history on a single central server, and each user's computer holds only the specific version they're currently working on. **Distributed** systems, like Git and Mercurial, give every user a complete copy of the entire project history on their own machine, so most operations (viewing history, creating branches, committing changes) happen locally without needing network access, and any user's copy can act as a full backup of the project. Distributed systems have become the dominant approach for their speed, resilience, and flexibility, which is why this Topic focuses on Git.

---

## 1.4 A Brief History Of Version Control

Early version control tools from the 1970s and 80s, like SCCS and RCS, tracked changes to individual files one at a time on a single machine. CVS, introduced in the late 1980s, extended this to track changes across an entire project and allowed multiple developers to work concurrently, though it remained centralized. Subversion improved on CVS's reliability and feature set through the 2000s. Git was created by Linus Torvalds in 2005 to manage the Linux kernel's source code, prioritizing speed, distributed workflows, and strong guarantees about data integrity — and its design, along with the rise of hosting platforms like GitHub, has made it the dominant version control system in use today.

[Previous](./[0]-Introduction-to-VersionControl.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[2]-Core-Version-Control-Concepts.md)
