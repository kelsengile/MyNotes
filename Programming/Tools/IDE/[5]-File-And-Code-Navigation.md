[Previous](./[4]-Projects-And-Workspaces.md) | [Table of Contents](./[0]-Introduction-to-IDEs.md) | [Next](./[6]-Build-Systems-And-Task-Runners.md)

*Project And Workspace Management*

# Lesson 5 - File And Code Navigation

## 5.1 The File Explorer

The file explorer, usually a sidebar panel, shows the folder and file structure of the open project as a tree. It lets you create, rename, move, and delete files without leaving the IDE, and clicking a file opens it in the editor. Most IDEs also let you filter or hide certain folders (like build output or dependency folders) so the tree stays readable.

---

## 5.2 Go To Definition And References

When reading unfamiliar code, you constantly need to answer "where is this actually defined?" and "where else is this used?" Go to Definition jumps straight to a symbol's declaration, while Find All References lists every usage across the project. Both work by having the IDE build an internal model of your code's structure rather than just searching text, which is why they stay accurate even as files move or get renamed.

---

## 5.3 Searching Across Files

A project-wide search (often bound to a shortcut like `Ctrl+Shift+F`) lets you find every occurrence of a piece of text across every file in the project, not just the one you have open. Most IDEs support searching with regular expressions and can also perform a find-and-replace across the whole project at once, which is useful for renaming something that isn't recognized as a single symbol.

---

## 5.4 Bookmarks And Outline Views

For long files, an **outline view** lists every function, class, or section in the current file so you can jump to one directly instead of scrolling. **Bookmarks** let you manually mark specific lines you want to return to later, independent of the file's structure — useful when you're partway through tracing a bug across several files and want to keep your place.

[Previous](./[4]-Projects-And-Workspaces.md) | [Table of Contents](./[0]-Introduction-to-IDEs.md) | [Next](./[6]-Build-Systems-And-Task-Runners.md)
