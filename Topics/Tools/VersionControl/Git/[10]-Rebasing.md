# 10. Rebasing & Interactive Rebase

## What Is Rebasing?

Rebasing takes the commits from one branch and re-applies them on top of another branch, creating a linear history instead of a merge commit.

```
Before:
main:     A -> B -> D
                \
feature:         C

After `git rebase main` (while on feature):
main:     A -> B -> D
                     \
feature:              C'   (C rewritten as a new commit C')
```

```bash
git switch feature
git rebase main
```

## Rebase vs. Merge

| | Merge | Rebase |
|---|---|---|
| History | Preserves exact history, includes merge commits | Rewrites commits onto a new base — linear history |
| Commit hashes | Unchanged | Changed for every rebased commit |
| Safe on shared branches | Yes | No, unless no one else has the old commits |
| Best for | Integrating a finished feature into `main` | Cleaning up a local feature branch before sharing |

**Golden rule of rebasing: never rebase commits that have already been pushed and that others may have based work on.**

## Basic Rebase Workflow

```bash
git switch feature-branch
git fetch origin
git rebase origin/main
```

If conflicts occur during rebase:
```bash
# fix the conflicting file(s)
git add resolved-file.txt
git rebase --continue

# or bail out entirely
git rebase --abort

# or skip this particular commit
git rebase --skip
```

## Interactive Rebase

Interactive rebase lets you edit, reorder, squash, or drop commits before they're finalized.

```bash
git rebase -i HEAD~5      # interactively edit the last 5 commits
git rebase -i main         # interactively edit all commits since diverging from main
```

This opens an editor with a list like:

```
pick a1b2c3d Add login form
pick e4f5g6h Fix typo in login form
pick h7i8j9k Add password validation

# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = combine with previous commit, merge messages
# f, fixup <commit> = combine with previous commit, discard this message
# d, drop <commit> = remove commit entirely
```

### Common Interactive Rebase Tasks

**Squash a "fix typo" commit into the one before it:**
```
pick a1b2c3d Add login form
fixup e4f5g6h Fix typo in login form
pick h7i8j9k Add password validation
```

**Reorder commits:** just reorder the lines.

**Edit an old commit's content:**
```
edit a1b2c3d Add login form
```
Git pauses at that commit — make your changes, then:
```bash
git add .
git commit --amend
git rebase --continue
```

**Reword a commit message without changing its content:**
```
reword a1b2c3d Add login form
```

**Drop a commit entirely:**
```
drop e4f5g6h Unwanted debug commit
```

## Rebasing onto a Different Base (`--onto`)

Useful for moving a chunk of commits from one branch base to another:

```bash
git rebase --onto main feature-old-base feature-branch
```

## After Rebasing a Pushed Branch

If you must rebase a branch that's already pushed (e.g. your own personal feature branch), you'll need to force-push:

```bash
git push --force-with-lease
```

## Summary

- Rebase re-applies commits on a new base, producing linear history — but changes commit hashes.
- Interactive rebase (`-i`) lets you squash, reword, reorder, edit, or drop commits.
- Never rebase shared/pushed commits others have built on top of.
- `--continue`, `--abort`, and `--skip` control an in-progress rebase.


