[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# tree

`tree` displays the contents of a directory as an indented, recursive tree diagram, making the shape of a nested folder structure immediately visible in a way a flat `ls -R` listing can't.

Reference: [https://oldmanprogrammer.net/source.php?dir=projects/tree](https://oldmanprogrammer.net/source.php?dir=projects/tree)

---

## What Is tree?

`tree` isn't part of POSIX or GNU Coreutils — it's a separate, small utility (originally written for DOS/OS2 in the early 1990s, later ported widely to Unix) that's usually installed separately (`apt install tree`, `brew install tree`, `choco install tree` on Windows, etc.). Its entire purpose is visualization: recursively walk a directory and print it using box-drawing characters (`├──`, `└──`, `│`) that mimic a family tree or file-manager sidebar, entirely in plain text.

---

## Core Commands

```bash
tree                            # Show the tree for the current directory
tree /path/to/folder             # Show the tree for a specific directory
tree -L 2                        # Limit depth to 2 levels
tree -d                          # Show directories only, no files
tree -a                          # Include hidden files (dotfiles)
tree -I "node_modules|.git"      # Ignore matching patterns
tree -f                          # Print full paths instead of just names
tree -h                          # Show human-readable file sizes next to entries
tree --du                        # Show cumulative directory sizes (like du)
tree -o output.txt               # Write the tree output to a file
```

## Full Option Reference

| Flag | Long form | Meaning |
|---|---|---|
| `-L N` | | Limit recursion to N levels deep |
| `-d` | | List directories only |
| `-a` | | Show hidden files too |
| `-f` | | Print the full path prefix for each file |
| `-I PATTERN` | | Ignore files/dirs matching a wildcard pattern |
| `-P PATTERN` | | Only list files matching a wildcard pattern |
| `-h` / `-s` | | Human-readable / raw byte sizes next to each file |
| `--du` | | Show accumulated directory size, like `du` |
| `-C` | | Force colorized output |
| `-J` | | Output as JSON |
| `-X` | | Output as XML |
| `--gitignore` | | Skip files ignored by a `.gitignore` |
| `-o FILE` | | Redirect the tree output to a file |

---

## Why Use tree Instead of ls -R?

`ls -R` lists every directory's contents recursively, but each subdirectory's listing is just a flat block of names with no visual indication of nesting depth or parent/child relationships. `tree` encodes that structure directly into the output using connecting lines, so a deeply nested project's shape is legible at a glance — genuinely useful for documentation, onboarding new developers to a codebase, or including a project's layout in a README.

---

## Machine-Readable Output

Beyond the visual tree, `tree` can emit structured formats meant for other programs to consume:

```bash
tree -J -L 2 > structure.json
tree -X > structure.xml
```

This turns `tree` into a lightweight filesystem-structure exporter — handy for feeding a directory layout into documentation generators, static site builders, or scripts that need to reason about a project's shape programmatically without shelling out to `find` and parsing its output themselves.

---

## Documenting a Project

A very common real use is dropping a `tree` snapshot directly into a README or design doc:

```bash
tree -L 2 -I "node_modules|.git|__pycache__"
```

```
myproject/
├── src/
│   ├── main.py
│   └── utils.py
├── tests/
│   └── test_main.py
├── README.md
└── requirements.txt
```

Excluding noisy, auto-generated directories like `node_modules` or `.git` keeps the documented structure focused on what matters to a human reader.

---

## Related Tools

- `ls -R` — the built-in, always-available recursive listing (no tree drawing).
- `find` — recursive search with far more filtering power, but flat output.
- `du --max-depth` — size-focused recursive summary without the visual tree.
- `exa --tree` / `eza --tree` — modern `ls` replacements that include a built-in tree mode plus color and Git status integration.

---

## Example Walkthrough

```bash
tree -L 2 -I "node_modules|.git" -h
```

Prints a two-level-deep tree of the current project, skipping dependency and version-control clutter, and annotating each file with a human-readable size — a quick visual health check of a codebase's top-level organization.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)