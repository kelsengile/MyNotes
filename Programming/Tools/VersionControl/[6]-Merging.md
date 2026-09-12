[Previous](./[5]-Branching.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[7]-Remote-Repositories.md)

*Tracking Changes*

# Lesson 6 - Merging

## 6.1 What Is Merging

Merging is the process of combining the changes from one branch into another, bringing two diverged lines of development back together. Running `git merge <branch>` while on your target branch (say, `main`) integrates all the commits made on `<branch>` since it diverged, producing a combined result that includes everyone's work. Merging is how the isolated work done on a feature branch (Lesson 5) eventually becomes part of the project's main line of development.

---

## 6.2 Fast-Forward vs Three-Way Merges

If the branch you're merging into hasn't had any new commits since the other branch diverged from it, Git can perform a **fast-forward merge**: it simply moves the target branch's pointer forward to match, since there's no actual divergence to reconcile. If both branches have new commits since they diverged, Git performs a **three-way merge**, comparing the two branch tips against their common ancestor commit and creating a new "merge commit" that has two parents, recording the point where the two lines of history rejoined. Three-way merges are what make Git's merge history show as a graph with branching and converging lines rather than a single straight line.

---

## 6.3 Merge Conflicts

A merge conflict occurs when the two branches being merged have made incompatible changes to the same part of a file — for example, both editing the exact same line differently — and Git can't automatically determine which version should win. When this happens, Git pauses the merge and marks the conflicting sections directly in the affected files using special markers (`<<<<<<<`, `=======`, `>>>>>>>`) showing both versions side by side, leaving it up to a person to decide how the conflict should be resolved.

---

## 6.4 Resolving Conflicts

To resolve a conflict, you edit the affected file to produce the correct final content — which might mean keeping one side's version, the other's, or a combination of both — then remove the conflict markers, stage the resolved file with `git add`, and complete the merge with `git commit`. Many editors and Git GUI tools provide visual merge conflict resolution that shows both versions side by side and lets you pick or combine them without manually editing the raw markers. Conflicts are a normal, expected part of collaborative version control rather than a sign something has gone wrong — they simply mean Git needs a human decision about how to reconcile two pieces of work.

[Previous](./[5]-Branching.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[7]-Remote-Repositories.md)
