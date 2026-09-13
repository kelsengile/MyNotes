[Previous](./[14]-Submodules.md) | [Table of Contents](./[0]-Introduction-to-Git.md) | [Next](./%5B16%5D-Blame-and-Bisect.md)

# Lesson 15 - Hooks

## 15.1 What Are Git Hooks?

Hooks are scripts that Git automatically runs at certain points in the Git workflow — before or after commits, pushes, merges, and more. They let you automate checks, formatting, notifications, or enforce rules.

Hooks live in `.git/hooks/` and are **not** version-controlled by default (since `.git/` itself isn't tracked) — a sample file exists for every hook type, suffixed `.sample`.

```bash
ls .git/hooks/
```
```
applypatch-msg.sample
commit-msg.sample
post-update.sample
pre-commit.sample
pre-push.sample
pre-rebase.sample
...
```

---

## 15.2 Enabling a Hook

Remove the `.sample` suffix and make it executable:

```bash
mv .git/hooks/pre-commit.sample .git/hooks/pre-commit
chmod +x .git/hooks/pre-commit
```

Hooks can be written in any language (bash, Python, Node, etc.) as long as the file has the correct shebang line and is executable.

---

## 15.3 Common Client-Side Hooks

| Hook | Runs |
|---|---|
| `pre-commit` | Before a commit message is even written — great for linting/formatting |
| `commit-msg` | After the message is written — validate its format |
| `post-commit` | After a commit completes — notifications, logging |
| `pre-push` | Before pushing — run tests, block push if they fail |
| `pre-rebase` | Before a rebase starts — prevent rebasing published branches |
| `post-checkout` | After switching branches — e.g. auto-install dependencies |
| `post-merge` | After a merge completes |

---

## 15.4 Example: `pre-commit` Hook (Linting)

```bash
#!/bin/sh
# .git/hooks/pre-commit
npx eslint . --max-warnings=0
if [ $? -ne 0 ]; then
  echo "Lint errors found. Commit aborted."
  exit 1
fi
```
Exiting non-zero blocks the commit; exiting 0 allows it to proceed.

---

## 15.5 Example: `commit-msg` Hook (Enforcing Format)

```bash
#!/bin/sh
# .git/hooks/commit-msg
commit_msg=$(cat "$1")
pattern="^(feat|fix|docs|chore|refactor)(\(.+\))?: .+"

if ! echo "$commit_msg" | grep -qE "$pattern"; then
  echo "Commit message must follow Conventional Commits format, e.g. 'feat: add login page'"
  exit 1
fi
```

---

## 15.6 Example: `pre-push` Hook (Running Tests)

```bash
#!/bin/sh
# .git/hooks/pre-push
npm test
if [ $? -ne 0 ]; then
  echo "Tests failed. Push aborted."
  exit 1
fi
```

---

## 15.7 Server-Side Hooks

These run on the remote server hosting the repository (relevant for self-hosted Git servers; hosted platforms like GitHub/GitLab expose similar functionality via their own webhook/Actions systems instead of raw server-side hooks):

| Hook | Runs |
|---|---|
| `pre-receive` | Before any refs are updated on the server — can reject the whole push |
| `update` | Once per branch being pushed — fine-grained per-branch rejection |
| `post-receive` | After the push completes — trigger deployments, notifications |

---

## 15.8 Sharing Hooks with a Team

Since `.git/hooks/` isn't committed, teams typically use one of:

1. **A hooks folder in the repo + a setup script** that copies/symlinks them into `.git/hooks/`.
   ```bash
   git config core.hooksPath .githooks
   ```
   This tells Git to look for hooks in a tracked `.githooks/` directory instead of `.git/hooks/`.

2. **Tools like Husky** (Node.js ecosystem) that manage hook installation automatically as part of `npm install`.

3. **Pre-commit framework** (Python-based, language-agnostic), configured via a `.pre-commit-config.yaml` file.

---

## 15.9 Bypassing Hooks

For emergencies (use sparingly):
```bash
git commit --no-verify -m "Emergency fix"
git push --no-verify
```

---

## 15.10 Summary

- Hooks are scripts triggered automatically at specific points in Git's workflow (commit, push, merge, etc.).
- They live in `.git/hooks/` by default and aren't tracked — share them via `core.hooksPath` or tools like Husky.
- Common uses: linting before commit, validating commit message format, running tests before push, triggering deployments after a server-side push.

---

[Previous](./[14]-Submodules.md) | [Table of Contents](./[0]-Introduction-to-Git.md) | [Next](./%5B16%5D-Blame-and-Bisect.md)
