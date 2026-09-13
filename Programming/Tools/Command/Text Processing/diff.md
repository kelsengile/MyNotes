[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

# diff

`diff` compares two files (or directories) line by line and reports exactly what's different between them. It's the underlying tool behind most version control systems' change tracking.

Download: [https://man7.org/linux/man-pages/man1/diff.1.html](https://man7.org/linux/man-pages/man1/diff.1.html)

---

## What Is diff?

Rather than just saying "these files differ," `diff` shows precisely which lines were added, removed, or changed, using a compact notation that tools like `patch`, and version control systems like Git, understand and can apply automatically.

---

## Core Commands

```bash
diff file1.txt file2.txt          # Show differences in diff's default format
diff -u file1.txt file2.txt       # Unified format — the format used in most patches and PRs
diff -r folder1/ folder2/         # Recursively compare two entire folders
diff -q file1.txt file2.txt       # Just report whether files differ, without details
```

---

## Reading Unified Diff Output

```diff
--- file1.txt
+++ file2.txt
@@ -1,3 +1,3 @@
 Hello there
-This is the old line.
+This is the new line.
 Goodbye.
```

Lines starting with `-` were removed, lines starting with `+` were added, and unmarked lines are unchanged context — this is the exact format you see in GitHub pull request diffs.

---

## Example Walkthrough

```bash
diff -u config.old.yaml config.new.yaml > changes.patch
```

Generates a unified diff between an old and new config file and saves it as a patch file, which could later be shared with a teammate or applied elsewhere with the `patch` command.

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
