[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# diff

`diff` compares two files (or directory trees) line by line and reports the differences between them. It's the foundational tool underneath nearly every modern code-review, patch, and version-control workflow.

Reference: [https://man7.org/linux/man-pages/man1/diff.1.html](https://man7.org/linux/man-pages/man1/diff.1.html)

---

## What Is diff?

At its core, `diff` solves the "longest common subsequence" problem: given two sequences of lines, find the smallest set of insertions and deletions that transforms one into the other, then describe that transformation. The output format it produces — a "diff" or "patch" — is compact and, crucially, machine-applicable: another tool (`patch`, or `git apply`) can take that same output and reproduce the transformation on a different copy of the original file.

This is why `diff` sits at the heart of collaborative software development: Git, Subversion, Mercurial, and code review tools like GitHub's pull-request view are all, underneath the UI, generating and rendering `diff`-style output.

---

## Core Commands

```bash
diff file1.txt file2.txt        # Show differences between two files
diff -u file1.txt file2.txt     # Unified diff format (the format used by patches and Git)
diff -c file1.txt file2.txt     # Context diff format (older, more verbose style)
diff -r dir1/ dir2/              # Recursively compare two directory trees
diff -q file1.txt file2.txt     # Just report whether files differ, don't show the diff itself
diff -i file1.txt file2.txt     # Ignore case differences
diff -w file1.txt file2.txt     # Ignore all whitespace differences
diff -b file1.txt file2.txt     # Ignore changes in amount of whitespace only
diff --color file1.txt file2.txt # Colorize the output
```

## Full Option Reference

| Flag | Long form | Meaning |
|---|---|---|
| `-u [N]` | `--unified[=N]` | Unified format with N lines of context (default 3) |
| `-c [N]` | `--context[=N]` | Context format with N lines of context |
| `-r` | `--recursive` | Compare directories recursively |
| `-q` | `--brief` | Report only whether files differ |
| `-i` | `--ignore-case` | Case-insensitive comparison |
| `-w` | `--ignore-all-space` | Ignore all whitespace |
| `-b` | `--ignore-space-change` | Treat runs of whitespace as equivalent |
| `-B` | `--ignore-blank-lines` | Ignore changes that only add/remove blank lines |
| `-N` | `--new-file` | Treat absent files in a directory comparison as empty |
| `-x PATTERN` | `--exclude=PATTERN` | Skip files matching a pattern during a directory diff |
| `--side-by-side` | `-y` | Show both files in two columns |

---

## Reading Unified Diff Output

```diff
--- file1.txt   2026-09-01
+++ file2.txt   2026-09-13
@@ -1,4 +1,4 @@
 Line one
-Old line two
+New line two
 Line three
 Line four
```

- Lines starting with `-` existed in the first file and were removed.
- Lines starting with `+` exist in the second file and were added.
- Unmarked lines are unchanged context, shown so a human (or `patch`) can locate where the change belongs.
- The `@@ -1,4 +1,4 @@` header ("hunk header") states which line ranges in each file the block below covers.

This exact format is what Git, GitHub, GitLab, and `git diff` all display — learning to read raw `diff -u` output directly translates to reading any code review.

---

## Beyond Two Files: Directory and Patch Workflows

- **`diff -r dir1/ dir2/`** compares entire trees, reporting added, removed, and changed files — useful for verifying a deployment matches a build, or checking what changed between two extracted archive versions.
- **Generating a patch**: `diff -u old.txt new.txt > change.patch` produces a file that can later be applied elsewhere with `patch -p1 < change.patch`, letting you ship a change without shipping the whole modified file.
- **Three-way diffs / merges** are handled by related tools (`diff3`, or Git's internal merge machinery) that extend the same line-comparison idea to reconcile two divergent versions of a common ancestor file.
- **Binary files**: `diff` detects binary content and, by default, just reports "Binary files … differ" rather than attempting a line-by-line comparison, since the concept of "lines" doesn't meaningfully apply.

---

## Related Tools

- `patch` — applies a `diff`-generated patch file to reconstruct changes.
- `git diff` / `git show` — Git's own diff engine, which adds move/rename detection and syntax-aware refinements on top of the same underlying algorithm.
- `vimdiff` / `meld` / `diff -y` — visual, side-by-side diff viewers for easier human reading.
- `cmp` — a simpler byte-by-byte comparison tool, useful for binary files where `diff`'s line-based model doesn't apply.
- `comm` — compares two *sorted* files line-by-line to find common and unique lines, a different but related use case.

---

## Example Walkthrough

```bash
diff -u old_config.yaml new_config.yaml > config.patch
patch old_config.yaml < config.patch
```

Generates a unified-format patch describing exactly what changed between two configuration files, then demonstrates applying that patch to reproduce the new version from the old one — the same mechanism used to ship and review code changes across the software industry.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)