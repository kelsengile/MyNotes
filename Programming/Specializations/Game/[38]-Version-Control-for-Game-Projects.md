[Previous](./[37]-Syncing-Game-State-Over-a-Network.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[39]-Asset-Pipelines-and-Import-Workflows.md)

*Tooling & Production*

# Lesson 38 - Version Control for Game Projects (Git, Git LFS)

## 38.1 Why Version Control Matters for Games

Version control tracks every change made to a project over time, letting a team collaborate without overwriting each other's work, and letting anyone roll back to a previous working state if something breaks. Game projects need this just as much as any other software project — but they also introduce a problem most software projects don't have: huge binary files.

---

## 38.2 Git Basics Recap

Git is the most widely used version control system. The core workflow:

- **Commit** — a saved snapshot of the project at a point in time, with a message describing the change.
- **Branch** — an independent line of development, letting a feature or fix be worked on without affecting the main project.
- **Merge** — combining changes from one branch into another.
- **Remote (e.g. GitHub, GitLab)** — a hosted copy of the repository that team members push to and pull from to share work.

---

## 38.3 The Binary File Problem and Git LFS

Git was designed for text files (code), where it can efficiently store just the *differences* between versions. Game projects are full of large binary assets — textures, 3D models, audio files — where Git can't meaningfully diff the content, so every change to a binary file effectively stores a whole new copy, bloating the repository quickly.

**Git LFS (Large File Storage)** solves this by storing large binary files outside the main repository, replacing them with small text pointers inside Git itself. This keeps the actual repository small and fast to clone, while still versioning the assets. Most teams configure Git LFS to automatically track common asset extensions (`.png`, `.fbx`, `.wav`, `.psd`, and so on).

---

## 38.4 .gitignore and Team Workflows

Game engines generate large amounts of auto-generated, machine-specific, or cache data (build outputs, temporary import caches, IDE settings) that should never be committed. A **`.gitignore`** file tells Git to ignore these paths entirely. Most engines provide a recommended starter `.gitignore` for exactly this reason.

Because binary assets generally can't be merged the way text/code can (two people editing the same texture at once produces a conflict Git can't resolve automatically), teams working on the same project typically adopt conventions like **locking files** while editing them, or assigning clear ownership of specific asset folders, to avoid stepping on each other's work.

[Previous](./[37]-Syncing-Game-State-Over-a-Network.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[39]-Asset-Pipelines-and-Import-Workflows.md)
