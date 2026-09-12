[Previous](./[6]-Merging.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[8]-Collaborative-Workflows.md)

*Collaboration*

# Lesson 7 - Remote Repositories

## 7.1 What Is A Remote

A remote is a version of a repository hosted somewhere other than your local machine, typically on a shared server or hosting platform, that you and your collaborators can push changes to and pull changes from. Since Git is distributed (Lesson 1), a remote isn't fundamentally different from any other copy of the repository — it's simply the copy everyone has agreed to treat as the shared, central point of coordination. A repository can have multiple remotes, though most projects work with just one, conventionally named `origin`.

```bash
git remote -v
# origin  https://github.com/example/my-project.git (fetch)
# origin  https://github.com/example/my-project.git (push)
```

---

## 7.2 Cloning A Repository

`git clone <url>` downloads a complete copy of a remote repository — its full history, all branches, and all commits — onto your local machine, automatically setting up that remote as `origin` for future syncing. This is typically the very first step when joining an existing project, and it gives you the same full, independent copy of the project's history that distributed version control promises, ready to branch, commit to, and work with entirely offline until you're ready to sync back up.

```bash
git clone https://github.com/example/my-project.git
cd my-project
git log --oneline    # the full history is already here, no network needed
```

---

## 7.3 Pushing And Pulling

Once you have local commits you want to share, `git push` uploads them to the remote, making them visible to everyone else working from that same remote. Conversely, `git pull` downloads commits made by others and integrates them into your current branch (under the hood, `git pull` is essentially `git fetch`, which downloads new commits without merging them, followed by a `git merge` to combine them into your work). Regularly pulling before starting new work, and pushing once it's ready, keeps a team's local copies from drifting too far out of sync with each other.

```bash
git push origin main     # upload your commits
git pull origin main     # download and merge others' commits

# equivalent to git pull, done as two explicit steps:
git fetch origin
git merge origin/main
```

---

## 7.4 Tracking Branches

A tracking branch is a local branch that's linked to a specific branch on a remote, so that Git knows exactly where to push and pull changes for it without you having to specify the remote and branch name every time. When you clone a repository, your local `main` branch is automatically set up to track `origin/main`. New branches you create can be linked to a remote counterpart with `git push -u origin <branch-name>`, after which plain `git push` and `git pull` commands know exactly where that branch's changes should go.

```bash
git switch -c feature/dark-mode
git push -u origin feature/dark-mode   # links local branch to origin/feature/dark-mode

# from now on, on this branch:
git push    # no need to specify origin/branch-name again
git pull
```

[Previous](./[6]-Merging.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md) | [Next](./[8]-Collaborative-Workflows.md)
