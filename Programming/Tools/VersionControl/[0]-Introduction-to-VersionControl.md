[⬅ Back to README](../../../README.md)

# Introduction to Version Control

Version control is the practice of tracking and managing changes to files over time, so that anyone can see what changed, when, why, and by whom — and, when needed, go back to any earlier state. This Topic builds up the fundamental, tool-agnostic concepts behind version control from the ground up: what a repository actually is, how changes are tracked and combined, how teams collaborate without stepping on each other's work, and how history is inspected, corrected, and maintained over the life of a project.

These lessons use Git — by far the most widely used version control system today — to illustrate the concepts concretely, but the underlying ideas (commits, branches, merging, conflict resolution) apply broadly across other version control systems as well. Once you understand the fundamentals here, you'll be equipped to work confidently with Git on any team, and to recognize the same ideas in other tools under different names.

## Why Study Version Control?

- **Mistakes become recoverable** — with a full history of changes, you can always go back to a working version instead of losing work or panicking over a bad edit.
- **It's how teams collaborate without chaos** — branching and merging let multiple people work on the same codebase at once without overwriting each other's changes.
- **It's a baseline expectation** — virtually every software job, open-source project, and coding bootcamp assumes you already know Git basics.
- **It documents the "why," not just the "what"** — commit messages and history turn a codebase into a record of decisions, useful long after you've forgotten the details.

Here's a quick comparison of the two broad approaches to version control you'll learn about:

| Aspect | Centralized VCS | Distributed VCS (e.g. Git) |
|---|---|---|
| History location | Single central server | Full copy on every machine |
| Working offline | Limited | Fully supported |
| Speed of commits | Depends on server connection | Instant (local) |
| Single point of failure | Yes (the central server) | No (every clone is a backup) |

## Version Control

The lessons above introduce the fundamentals of version control, including tracking changes, managing different versions of files, and collaborating on software projects. To see these concepts applied to a widely-used version control system, continue on to:

* **[Git](https://git-scm.com)** — a distributed version control system used to track changes in source code, manage project history, create branches, and support collaboration among developers.

## Table of Contents

**Foundations**

   1. **[What Is Version Control?](./[1]-What-Is-Version-Control.md)**  
       1.1 Defining Version Control  
       1.2 Why Version Control Matters  
       1.3 Centralized vs Distributed Version Control  
       1.4 A Brief History Of Version Control  
   2. **[Core Version Control Concepts](./[2]-Core-Version-Control-Concepts.md)**  
       2.1 Repositories  
       2.2 Commits And Snapshots  
       2.3 The Working Directory, Staging Area, And History  
       2.4 Diffs And Changesets  
   3. **[Getting Started With Git](./[3]-Getting-Started-With-Git.md)**  
       3.1 Installing And Configuring Git  
       3.2 Initializing A Repository  
       3.3 The Git Workflow  
       3.4 Ignoring Files  

**Tracking Changes**

   4. **[Committing Changes](./[4]-Committing-Changes.md)**  
       4.1 Staging Changes  
       4.2 Writing Good Commit Messages  
       4.3 Viewing History  
       4.4 Undoing And Amending Commits  
   5. **[Branching](./[5]-Branching.md)**  
       5.1 What Is A Branch  
       5.2 Creating And Switching Branches  
       5.3 Branching Strategies  
       5.4 Deleting And Renaming Branches  
   6. **[Merging](./[6]-Merging.md)**  
       6.1 What Is Merging  
       6.2 Fast-Forward vs Three-Way Merges  
       6.3 Merge Conflicts  
       6.4 Resolving Conflicts  

**Collaboration**

   7. **[Remote Repositories](./[7]-Remote-Repositories.md)**  
       7.1 What Is A Remote  
       7.2 Cloning A Repository  
       7.3 Pushing And Pulling  
       7.4 Tracking Branches  
   8. **[Collaborative Workflows](./[8]-Collaborative-Workflows.md)**  
       8.1 Centralized Workflow  
       8.2 Feature Branch Workflow  
       8.3 Forking Workflow  
       8.4 Trunk-Based Development  
   9. **[Pull Requests And Code Review](./[9]-Pull-Requests-And-Code-Review.md)**  
       9.1 What Is A Pull Request  
       9.2 The Code Review Process  
       9.3 Review Etiquette  
       9.4 Merging A Pull Request  

**Advanced Techniques**

   10. **[Rebasing](./[10]-Rebasing.md)**  
       10.1 What Is Rebasing  
       10.2 Rebase vs Merge  
       10.3 Interactive Rebase  
       10.4 The Golden Rule Of Rebasing  
   11. **[Stashing And Cherry-Picking](./[11]-Stashing-And-Cherry-Picking.md)**  
       11.1 Stashing Changes  
       11.2 Cherry-Picking Commits  
       11.3 When To Use Each  
       11.4 Common Pitfalls  
   12. **[Tags And Releases](./[12]-Tags-And-Releases.md)**  
       12.1 What Is A Tag  
       12.2 Lightweight vs Annotated Tags  
       12.3 Semantic Versioning  
       12.4 Creating Releases  

**Maintaining History**

   13. **[Undoing Changes](./[13]-Undoing-Changes.md)**  
       13.1 Reset vs Revert vs Checkout  
       13.2 Soft, Mixed, And Hard Reset  
       13.3 Reverting Public Commits  
       13.4 Recovering Lost Work With Reflog  
   14. **[Rewriting History](./[14]-Rewriting-History.md)**  
       14.1 Amending Commits  
       14.2 Squashing Commits  
       14.3 Filtering And Rewriting Large Histories  
       14.4 Risks Of Rewriting Shared History  
   15. **[Submodules And Monorepos](./[15]-Submodules-And-Monorepos.md)**  
       15.1 What Is A Submodule  
       15.2 Working With Submodules  
       15.3 Monorepos  
       15.4 Choosing An Approach  

**Best Practices And Beyond**

   16. **[Git Best Practices](./[16]-Git-Best-Practices.md)**  
       16.1 Commit Hygiene  
       16.2 Branch Naming Conventions  
       16.3 .gitignore And Sensitive Data  
       16.4 Protecting Important Branches  
   17. **[Version Control Hosting Platforms](./[17]-Version-Control-Hosting-Platforms.md)**  
       17.1 GitHub  
       17.2 GitLab  
       17.3 Bitbucket  
       17.4 Self-Hosted Options  

**Next Steps**

   18. **[Choosing And Exploring Version Control Tools](./[18]-Choosing-And-Exploring-Version-Control-Tools.md)**  
       18.1 Other Version Control Systems  
       18.2 Git GUIs And Editor Integrations  
       18.3 Learning Resources  
       18.4 Where To Go Next  