[⬅ Back to README](../../../README.md)

# Git


Welcome! This is a self-paced course for learning Git, the version control system used by most software teams to track changes to code and collaborate without stepping on each other's work.

## What is Git?

Git lets you:
- Save snapshots ("commits") of your project over time
- Go back to any previous snapshot
- Work on experimental changes ("branches") without breaking your main code
- Collaborate with others and merge everyone's work together

## Table of Contents

1. **[Installing Git & First-Time Setup](./[1]-Installation-and-Setup.md)**  
   1.1 What is Git?  
   1.2 Installing Git  
   1.3 Verifying Installation  
   1.4 First-Time Setup  
   1.5 SSH Keys (Optional but Recommended)  
   1.6 Summary  
2. **[Git Basics](./[2]-Git-Basics.md)**  
   2.1 The Three (Four) Areas of Git  
   2.2 Creating a Repository  
   2.3 The Basic Workflow  
   2.4 Writing Good Commit Messages  
   2.5 Tracked vs. Untracked Files  
   2.6 A Minimal Example Session  
   2.7 Summary  
3. **[Checking Status & Viewing History](./%5B3%5D-Status-and-History.md)**  
   3.1 `git status`  
   3.2 `git diff`  
   3.3 `git log`  
   3.4 `git show`  
   3.5 Referring to Commits  
   3.6 Summary  
4. **[Branching](./[4]-Branching.md)**  
   4.1 What Is a Branch?  
   4.2 Viewing Branches  
   4.3 Creating a Branch  
   4.4 Switching Branches  
   4.5 Renaming a Branch  
   4.6 Deleting a Branch  
   4.7 Comparing Branches  
   4.8 A Typical Feature Branch Workflow  
   4.9 Branch Naming Conventions  
   4.10 What "Current Branch" Really Means  
   4.11 Detached HEAD State  
   4.12 Summary  
5. **[Merging & Resolving Conflicts](./%5B5%5D-Merging-and-Conflicts.md)**  
   5.1 What Is a Merge?  
   5.2 Fast-Forward Merge  
   5.3 Three-Way Merge  
   5.4 Merge Conflicts  
   5.5 Using a Merge Tool  
   5.6 Tips to Avoid Conflicts  
   5.7 Summary  
6. **[Working with Remotes, Pushing & Pulling](./%5B6%5D-Remotes-Push-and-Pull.md)**  
   6.1 What Is a Remote?  
   6.2 Viewing Remotes  
   6.3 Adding and Removing Remotes  
   6.4 Pushing  
   6.5 Fetching  
   6.6 Pulling  
   6.7 Tracking Branches  
   6.8 Cloning vs. Adding a Remote  
   6.9 Working with Forks  
   6.10 Summary  
7. **[Undoing Changes & Rewriting History](./%5B7%5D-Undoing-and-Rewriting-History.md)**  
   7.1 Undoing Uncommitted Changes  
   7.2 Undoing Commits  
   7.3 `reset` vs. `revert` — Which to Use?  
   7.4 Rewriting History (Advanced)  
   7.5 Recovering "Lost" Work  
   7.6 Summary  
8. **[.gitignore & Housekeeping](./[8]-Gitignore.md)**  
   8.1 What Is `.gitignore`?  
   8.2 Basic Pattern Syntax  
   8.3 Common `.gitignore` Templates  
   8.4 Ignoring Files Already Tracked  
   8.5 Global .gitignore (Across All Repos)  
   8.6 Checking Why a File Is Ignored  
   8.7 Other Housekeeping Commands  
   8.8 Summary  
9. **[Common Workflows & Best Practices](./%5B9%5D-Workflows-and-Best-Practices.md)**  
   9.1 Popular Branching Models  
   9.2 Commit Best Practices  
   9.3 Pull Request / Code Review Best Practices  
   9.4 Handling Merge Conflicts Gracefully  
   9.5 Protecting Important Branches  
   9.6 Semantic / Conventional Commits (Optional Convention)  
   9.7 General Tips  
   9.8 Summary  
10. **[Rebasing & Interactive Rebase](./[10]-Rebasing.md)**  
    10.1 What Is Rebasing?  
    10.2 Rebase vs. Merge  
    10.3 Basic Rebase Workflow  
    10.4 Interactive Rebase  
    10.5 Rebasing onto a Different Base (`--onto`)  
    10.6 After Rebasing a Pushed Branch  
    10.7 Summary  
11. **[Stashing](./[11]-Stashing.md)**  
    11.1 What Is Stashing?  
    11.2 Basic Usage  
    11.3 Naming and Managing Multiple Stashes  
    11.4 Stashing Specific Files  
    11.5 Including Untracked or Ignored Files  
    11.6 Creating a Branch from a Stash  
    11.7 A Typical Scenario  
    11.8 Stash Conflicts  
    11.9 Summary  
12. **[Tags & Cherry-Picking](./%5B12%5D-Tags-and-Cherry-Picking.md)**  
    12.1 Part 1: Tags  
    12.2 Part 2: Cherry-Picking  
    12.3 Summary  
13. **[Reflog: Recovering Lost Commits](./[13]-Reflog.md)**  
    13.1 What Is the Reflog?  
    13.2 Viewing the Reflog  
    13.3 Recovering a "Lost" Commit  
    13.4 Using `HEAD@{N}` Directly  
    13.5 Finding Dangling Commits  
    13.6 Reflog Expiration  
    13.7 What the Reflog Can't Save You From  
    13.8 Summary  
14. **[Submodules](./[14]-Submodules.md)**  
    14.1 What Is a Submodule?  
    14.2 Adding a Submodule  
    14.3 Cloning a Repo That Has Submodules  
    14.4 Updating a Submodule  
    14.5 Checking Submodule Status  
    14.6 Working Inside a Submodule  
    14.7 Removing a Submodule  
    14.8 Submodules vs. Alternatives  
    14.9 Common Pitfalls  
    14.10 Summary  
15. **[Hooks](./[15]-Hooks.md)**  
    15.1 What Are Git Hooks?  
    15.2 Enabling a Hook  
    15.3 Common Client-Side Hooks  
    15.4 Example: `pre-commit` Hook (Linting)  
    15.5 Example: `commit-msg` Hook (Enforcing Format)  
    15.6 Example: `pre-push` Hook (Running Tests)  
    15.7 Server-Side Hooks  
    15.8 Sharing Hooks with a Team  
    15.9 Bypassing Hooks  
    15.10 Summary  
16. **[Blame & Bisect: Finding Bugs](./%5B16%5D-Blame-and-Bisect.md)**  
    16.1 Part 1: `git blame`  
    16.2 Part 2: `git bisect`  
    16.3 Combining Blame and Bisect  
    16.4 Summary  
17. **[Aliases & Configuration](./%5B17%5D-Aliases-and-Config.md)**  
    17.1 Configuration Recap  
    17.2 Viewing and Editing Config  
    17.3 Creating Aliases  
    17.4 Useful Config Options Beyond Aliases  
    17.5 Per-Repository Config (Overrides Global)  
    17.6 Environment Variables  
    17.7 Summary  
18. **[Pull Requests & Code Review Workflow](./[18]-Pull-Requests.md)**  
    18.1 What Is a Pull Request?  
    18.2 Typical PR Workflow  
    18.3 Writing a Good PR Description  
    18.4 Responding to Review Feedback  
    18.5 Merge Strategies on Hosting Platforms  
    18.6 Keeping a PR Up to Date with `main`  
    18.7 Code Review Best Practices  
    18.8 Draft Pull Requests  
    18.9 Linking Issues  
    18.10 Summary  
19. **[Git Internals: Objects, Refs, and the .git Directory](./[19]-Git-Internals.md)**  
    19.1 The `.git` Directory  
    19.2 Git's Object Model  
    19.3 How a Commit Is Actually Created  
    19.4 Refs  
    19.5 Packfiles  
    19.6 The Index (Staging Area)  
    19.7 Low-Level ("Plumbing") vs. High-Level ("Porcelain") Commands  
    19.8 Why This Matters  
    19.9 Summary  