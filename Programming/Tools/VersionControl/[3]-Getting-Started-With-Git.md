[Previous](./[2]-Core-Version-Control-Concepts.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[4]-Committing-Changes.md)

*Foundations*

# Lesson 3 - Getting Started With Git

## 3.1 Installing And Configuring Git

Git can be installed on Windows, macOS, and Linux through an official installer, a package manager, or bundled developer tools. Once installed, a small amount of one-time configuration tells Git who you are, since that information is recorded in every commit you make:

```
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

The `--global` flag applies these settings to every repository on your machine; they can be overridden per-project if needed. Other common configuration options include your preferred text editor for writing commit messages and your default branch name.

---

## 3.2 Initializing A Repository

A new Git repository is created with a single command run inside a project folder:

```
git init
```

This creates the hidden `.git` folder that will hold the project's entire history, without affecting any of the files already in the directory. From this point forward, Git is watching the folder and ready to track whatever changes you choose to stage and commit — though it won't record anything automatically until you explicitly tell it to.

---

## 3.3 The Git Workflow

Day-to-day work in Git follows a repeating cycle: edit files in the working directory, review what changed with `git status` and `git diff`, stage the changes you want to include with `git add`, and commit them with `git commit` to permanently record a snapshot. This cycle repeats continuously throughout a project's life, with branching (Lesson 5) and merging (Lesson 6) layered on top once more than one line of development is involved. Getting comfortable with this basic loop — edit, stage, commit — is the foundation everything else in this Topic builds on.

---

## 3.4 Ignoring Files

Not every file in a project folder belongs in version control — build artifacts, dependency folders, log files, and files containing secrets or local configuration typically shouldn't be tracked. A `.gitignore` file, placed at the root of a repository, lists patterns for files and folders Git should simply ignore, so they never show up as untracked changes and can't be accidentally committed. A typical entry might look like `node_modules/` to ignore an entire dependency folder, or `*.log` to ignore any file ending in `.log`. Setting up a thoughtful `.gitignore` early in a project keeps the repository's history clean and avoids accidentally committing sensitive or unnecessary files.

[Previous](./[2]-Core-Version-Control-Concepts.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[4]-Committing-Changes.md)
