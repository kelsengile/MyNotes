[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# tree

`tree` displays the contents of a directory as a visual, hierarchical tree, making it much easier to see a project's overall structure at a glance than a flat `ls` listing.

Download: [https://mama.indstate.edu/users/ice/tree/](https://mama.indstate.edu/users/ice/tree/)

---

## What Is tree?

Unlike `ls`, which lists one folder's contents at a time, `tree` recursively walks into every subfolder and draws the whole structure as connected branches — the same kind of diagram you'd sketch on paper to explain a project's layout.

---

## Core Commands

```bash
tree                     # Show the tree for the current directory
tree /path/to/folder      # Show the tree for a specific folder
tree -L 2                 # Limit the depth to 2 levels
tree -d                    # Show only directories, no files
tree -a                    # Include hidden files and folders
tree -I "node_modules"     # Exclude a matching pattern from the output
```

---

## Sample Output

```
project/
├── src/
│   ├── main.py
│   └── utils.py
├── tests/
│   └── test_main.py
└── README.md
```

This kind of output is often pasted directly into a README to give newcomers an instant sense of a project's layout.

---

## Example Walkthrough

```bash
tree -L 2 -I "node_modules|.git"
```

Shows the project's structure two levels deep while hiding both `node_modules` and `.git` — the two folders that would otherwise clutter the output with noise nobody needs to see.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
