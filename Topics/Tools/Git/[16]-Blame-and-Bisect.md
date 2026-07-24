# 16. Blame & Bisect: Finding Bugs

## Part 1: `git blame`

### What Is `git blame`?

`git blame` shows, line by line, which commit last modified each line of a file, along with the author and date. Useful for understanding *why* a piece of code exists or *who* to ask about it.

```bash
git blame file.txt
```
```
a1b2c3d4 (Jane Doe 2026-06-01 10:15:00 +0800  1) function calculateTotal(items) {
e4f5g6h7 (John Smith 2026-06-15 14:22:00 +0800  2)   return items.reduce((sum, i) => sum + i.price, 0);
a1b2c3d4 (Jane Doe 2026-06-01 10:15:00 +0800  3) }
```

### Useful Options

```bash
git blame -L 10,20 file.txt        # only show lines 10-20
git blame -w file.txt               # ignore whitespace changes
git blame -M file.txt                # detect moved lines within the file
git blame -C file.txt                # detect lines copied from other files
git blame --since="2 weeks ago" file.txt
```

### Following History Through Renames

```bash
git log --follow file.txt
```

### Blaming a Specific Commit Range

```bash
git blame a1b2c3d..e4f5g6h -- file.txt
```

### Ignoring a Specific Commit (e.g. a big reformatting commit)

Useful when a mass reformat makes blame less useful for finding the "real" author:

```bash
git blame --ignore-rev <reformat-commit-hash> file.txt
```
Or maintain a permanent ignore list:
```bash
git config blame.ignoreRevsFile .git-blame-ignore-revs
```

## Part 2: `git bisect`

### What Is `git bisect`?

`git bisect` performs a binary search through your commit history to find the exact commit that introduced a bug — extremely useful when you know a bug exists now, know a past commit where it didn't, but don't know where in between it was introduced.

### Starting a Bisect Session

```bash
git bisect start
git bisect bad                  # current commit is broken
git bisect good v1.2.0           # this earlier commit/tag was known good
```

Git checks out a commit roughly halfway between "good" and "bad" and asks you to test it.

```bash
# ... test the code ...
git bisect good      # if the bug is NOT present here
git bisect bad        # if the bug IS present here
```

Repeat until Git narrows it down:
```
a1b2c3d is the first bad commit
```

### Ending the Session

```bash
git bisect reset
```
Returns you to the branch/commit you were on before starting.

### Automating Bisect with a Test Script

If you have a script or test command that exits `0` for good and non-zero for bad, Git can run the entire bisect automatically:

```bash
git bisect start
git bisect bad HEAD
git bisect good v1.2.0
git bisect run npm test
```

Git will run the test at each step and automatically mark it good/bad, finishing with the culprit commit identified — no manual testing needed.

### Skipping Untestable Commits

Sometimes a commit in the range can't be tested (e.g. it doesn't build). Skip it:
```bash
git bisect skip
```

### Viewing Bisect Progress

```bash
git bisect log
```
Can be saved and replayed later:
```bash
git bisect log > bisect-log.txt
git bisect replay bisect-log.txt
```

## Combining Blame and Bisect

A typical debugging flow:
1. Use `git bisect` to identify the exact commit that introduced a regression.
2. Use `git show <commit>` or `git blame` on the affected file to understand what changed and why.
3. Reach out to the author (found via blame) if more context is needed, or `git revert`/fix directly.

## Summary

- `git blame` shows who last changed each line of a file and when — great for context, not for "fault."
- `git bisect` binary-searches commit history to pinpoint exactly which commit introduced a bug.
- `git bisect run` automates the whole process if you have a scriptable pass/fail test.

