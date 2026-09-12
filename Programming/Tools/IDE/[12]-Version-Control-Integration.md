[Previous](./[11]-Themes-And-Editor-Customization.md) | [Table of Contents](./[0]-Introduction-to-IDEs.md) | [Next](./[13]-Remote-And-Collaborative-Development.md)

*Collaboration And Version Control*

# Lesson 12 - Version Control Integration

## 12.1 Git Integration Basics

Most IDEs detect when a project is tracked by Git (or another version control system) and surface common actions directly in the interface — staging changes, writing a commit message, committing, pushing, and pulling — without needing to type Git commands. Changed files are usually marked with a colored icon (added, modified, deleted) both in the file explorer and in the editor's margin, next to the exact lines that changed.

| Icon/marker | Meaning |
|---|---|
| Green bar (gutter) | Lines added |
| Blue bar (gutter) | Lines modified |
| Red triangle (gutter) | Lines deleted just above this point |
| "M" badge (file explorer) | File modified since last commit |
| "U" badge (file explorer) | Untracked file, not yet added to Git |

---

## 12.2 Diff Views And Merge Conflicts

A diff view shows exactly what changed in a file — old lines removed, new lines added — usually with a side-by-side or inline comparison. When two changes conflict during a merge, IDEs typically offer a dedicated conflict-resolution view that lets you choose which version of a conflicting section to keep, or combine both, without hand-editing the raw conflict markers Git inserts into the file.

**Example raw conflict Git would otherwise leave in a file:**

```
<<<<<<< HEAD
const taxRate = 0.08;
=======
const taxRate = 0.075;
>>>>>>> feature/updated-tax
```

An IDE's merge view shows this as two clearly labeled versions side by side with "Accept Current," "Accept Incoming," or "Accept Both" buttons — rather than requiring you to manually delete the `<<<<<<<`, `=======`, and `>>>>>>>` markers yourself.

---

## 12.3 Commit History And Blame

A history view lets you browse past commits, see what changed in each one, and search commit messages. A **blame** view (sometimes called annotations) shows, line by line, which commit last changed each line and who authored it — useful for understanding why a particular piece of code exists before you change it.

**Example blame output for a single line:**

```
a3f9c21  (Priya Shah, 3 months ago)  const MAX_RETRIES = 5;
```

Seeing who last touched the line and when — rather than just the line itself — often points you straight to the commit message or pull request that explains *why* the retry limit is 5 instead of some other number.

---

## 12.4 Pull Requests In The IDE

Many IDEs and their extensions let you create, review, and comment on pull requests without leaving the editor, pulling in the diff, discussion thread, and CI status from services like GitHub or GitLab. This keeps code review in the same environment as writing code, rather than requiring a constant switch to a browser.

| Task | Without IDE integration | With IDE integration |
|---|---|---|
| Read a PR's diff | Open browser, load PR page | View inline in the editor |
| Leave a review comment | Click through the browser UI | Comment directly on the line |
| Check CI status | Refresh the browser tab | See a status badge in the IDE |

---

[Previous](./[11]-Themes-And-Editor-Customization.md) | [Table of Contents](./[0]-Introduction-to-IDEs.md) | [Next](./[13]-Remote-And-Collaborative-Development.md)
