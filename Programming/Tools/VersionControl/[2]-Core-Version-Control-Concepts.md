[Previous](./[1]-What-Is-Version-Control.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[3]-Getting-Started-With-Git.md)

*Foundations*

# Lesson 2 - Core Version Control Concepts

## 2.1 Repositories

A repository (often shortened to "repo") is the container that holds a project's files along with the complete history of every change ever made to them. In Git, a repository lives in a hidden `.git` folder at the root of a project directory, storing all commits, branches, and configuration needed to reconstruct the project's history — the visible project files you edit are just the current "checked out" snapshot of that history. Because a Git repository is self-contained, an entire project's history can be copied, backed up, or moved simply by copying that folder.

---

## 2.2 Commits And Snapshots

A commit is a recorded snapshot of the entire project at a specific point in time, along with metadata like the author, timestamp, and a message describing what changed and why. Rather than storing a full copy of every file for every commit, Git stores each commit as a snapshot of the whole project and efficiently reuses unchanged file content between commits, keeping storage compact even across a long history. Commits are linked together in a chain, each one pointing back to the commit that came before it, forming the project's full history as a connected graph rather than a flat list.

---

## 2.3 The Working Directory, Staging Area, And History

Git organizes a project's state into three conceptual areas. The **working directory** is the actual files on disk that you edit directly. The **staging area** (or "index") is a holding area where you place specific changes you intend to include in your next commit — this lets you build a commit out of exactly the changes you want, even if your working directory has other, unrelated edits in progress. The **repository history** is the permanent record of commits already made. Changes flow in one direction: you edit files in the working directory, stage the specific changes you want with `git add`, and then commit the staged changes to permanently record them in history.

---

## 2.4 Diffs And Changesets

A diff is a representation of the differences between two versions of a file or project — typically shown as lines added, removed, or modified. Diffs are how Git (and the humans reviewing its output) understand what actually changed between any two points in history, whether that's your uncommitted edits versus the last commit, or two arbitrary commits far apart in time. A changeset refers to the full set of changes bundled into a single commit, effectively the diff between that commit and its parent. Reading diffs is one of the most common day-to-day activities in version control, whether reviewing your own work before committing or reviewing a teammate's proposed changes.

[Previous](./[1]-What-Is-Version-Control.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[3]-Getting-Started-With-Git.md)
