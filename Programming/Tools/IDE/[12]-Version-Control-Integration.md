[Previous](./[11]-Themes-And-Editor-Customization.md) | [Table of Contents](./[0]-Introduction-to-IDEs.md) | [Next](./[13]-Remote-And-Collaborative-Development.md)

*Collaboration And Version Control*

# Lesson 12 - Version Control Integration

## 12.1 Git Integration Basics

Most IDEs detect when a project is tracked by Git (or another version control system) and surface common actions directly in the interface — staging changes, writing a commit message, committing, pushing, and pulling — without needing to type Git commands. Changed files are usually marked with a colored icon (added, modified, deleted) both in the file explorer and in the editor's margin, next to the exact lines that changed.

---

## 12.2 Diff Views And Merge Conflicts

A diff view shows exactly what changed in a file — old lines removed, new lines added — usually with a side-by-side or inline comparison. When two changes conflict during a merge, IDEs typically offer a dedicated conflict-resolution view that lets you choose which version of a conflicting section to keep, or combine both, without hand-editing the raw conflict markers Git inserts into the file.

---

## 12.3 Commit History And Blame

A history view lets you browse past commits, see what changed in each one, and search commit messages. A **blame** view (sometimes called annotations) shows, line by line, which commit last changed each line and who authored it — useful for understanding why a particular piece of code exists before you change it.

---

## 12.4 Pull Requests In The IDE

Many IDEs and their extensions let you create, review, and comment on pull requests without leaving the editor, pulling in the diff, discussion thread, and CI status from services like GitHub or GitLab. This keeps code review in the same environment as writing code, rather than requiring a constant switch to a browser.

[Previous](./[11]-Themes-And-Editor-Customization.md) | [Table of Contents](./[0]-Introduction-to-IDEs.md) | [Next](./[13]-Remote-And-Collaborative-Development.md)
