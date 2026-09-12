[Previous](./[17]-Version-Control-Hosting-Platforms.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md)

*Next Steps*

# Lesson 18 - Choosing And Exploring Version Control Tools

## 18.1 Other Version Control Systems

While Git dominates the industry today, other version control systems remain in active use and are worth knowing about. **Mercurial** is another distributed system, conceptually similar to Git but with a design that many find simpler and more consistent, and still sees use in some large organizations and open-source projects. **Subversion (SVN)**, a centralized system, remains in use in some enterprise environments with long-established workflows built around it. **Perforce** is common in industries dealing with very large binary assets, like game development, where Git's design is less well suited. Understanding the core concepts from this Topic — commits, branches, merging, conflict resolution — transfers well to any of these, even though the specific commands differ.

The core concepts map across tools even when the commands don't:

| Concept | Git | Mercurial | Subversion |
|---|---|---|---|
| Save a snapshot | `git commit` | `hg commit` | `svn commit` |
| See history | `git log` | `hg log` | `svn log` |
| Create a branch | `git branch` | `hg branch` | `svn copy` (to a branches folder) |
| Get latest changes | `git pull` | `hg pull -u` | `svn update` |

---

## 18.2 Git GUIs And Editor Integrations

While this Topic has focused on Git's command line, plenty of graphical tools exist for people who prefer a visual interface — standalone GUI clients like GitHub Desktop, Sourcetree, and GitKraken, as well as Git integrations built directly into code editors like VS Code, IntelliJ, and others. These tools can make certain operations (like reviewing a diff, staging specific hunks of a file, or resolving a merge conflict) more visual and approachable, especially for newcomers. Most experienced developers use some blend of both — the command line for its speed and scriptability, and GUI tools for tasks that benefit from a visual view of the repository's state.

| Tool type | Examples | Where it shines |
|---|---|---|
| Standalone GUI | GitHub Desktop, Sourcetree, GitKraken | Visualizing branch history, staging individual hunks |
| Editor-integrated | VS Code's Source Control tab, IntelliJ's VCS tools | Reviewing diffs without leaving your code |
| Command line | `git` itself | Speed, scripting, and access to every feature |

---

## 18.3 Learning Resources

To go deeper on Git and version control, the official *Pro Git* book (freely available online) covers everything in this Topic in significantly more depth, including Git's internal object model. Git's own built-in documentation, accessible via `git help <command>` or `man git-<command>`, is thorough and always matches the exact version installed on your machine. Beyond reading, the best way to build real fluency is deliberate practice: create a throwaway repository, and practice branching, merging, causing and resolving conflicts on purpose, and experimenting with reset, revert, and rebase in a environment where mistakes cost nothing.

A safe way to set up that kind of sandbox in seconds:

```bash
mkdir git-sandbox && cd git-sandbox
git init
echo "hello" > file.txt
git add file.txt
git commit -m "Initial commit"
# now branch, merge, conflict, and reset freely — nothing here matters
```

---

## 18.4 Where To Go Next

With the fundamentals in this Topic in hand, you're equipped to collaborate confidently on real software projects, contribute to open-source repositories using the forking workflow, and explore more advanced Git internals (like how commits, trees, and blobs are actually stored as objects) if you want to understand the system at a deeper level. Version control is also foundational to modern software practices like continuous integration and continuous deployment (CI/CD), which build automated pipelines directly on top of the branching and merging concepts covered here — a natural next area to explore once these fundamentals feel comfortable.

Some concrete "next" directions, depending on what interests you most:

- **Curious about internals?** Explore how Git stores data with `git cat-file -p <hash>` on a commit, tree, and blob to see the object model directly.
- **Curious about collaboration at scale?** Contribute a small fix to a real open-source project using the forking workflow from Lesson 8.
- **Curious about automation?** Set up a basic CI pipeline (GitHub Actions, GitLab CI) that runs your test suite on every push.
- **Curious about tooling?** Try one of the GUI clients from 18.2 alongside the command line and see which operations feel better visually.

[Previous](./[17]-Version-Control-Hosting-Platforms.md) | [Table of Contents](./[0]-Introduction-to-VersionControl.md)
