[Previous](./[5]-Extensions-And-Customization.md) | [Table of Contents](./[0]-Introduction-to-VisualStudioCode.md) | [Next](./[7]-Tasks,-Snippets,-And-Remote-Development.md)

*Development Workflows*

# Lesson 6 - Source Control With Git In VS Code

## 6.1 The Source Control Panel

VS Code bundles Git support natively — no extension required for the basics (GitLens and others add extras, covered in 6.4). Open it via the Activity Bar's 🌿 icon or `Ctrl/Cmd+Shift+G`.

```
┌───────────────────────────────────────┐
│ SOURCE CONTROL                          │
├───────────────────────────────────────┤
│ Message: [Fix null pointer in login  ] │
│ [✓ Commit]                              │
│                                          │
│ Changes (3)                             │
│   M  src/login.js                       │
│   M  src/auth.js                        │
│   U  src/newFile.js  (Untracked)         │
└───────────────────────────────────────┘
```

The letter badges next to each file are Git status codes:

| Badge | Meaning |
|---|---|
| M | Modified |
| U | Untracked (new, never added) |
| A | Added (staged, new file) |
| D | Deleted |
| C | Conflict |

The gutter next to line numbers in the editor also shows colored bars — a green bar for added lines, blue for modified, and a red triangle marker for deleted lines — exactly mirroring the JetBrains editor gutter covered in the JetBrains Topic's Version Control lesson.

---

## 6.2 Staging, Committing, And Syncing

Git's staging area is represented explicitly in the panel: files sit under "Changes" until you stage them (moving them to "Staged Changes"), and only staged files are included in the next commit.

```
Changes                  Staged Changes
  M login.js      [+]  →   M login.js
  M auth.js                
```

- **Stage a file** — hover over it and click the `+` icon, or stage everything at once with the `+` next to the "Changes" header.
- **Commit** — type a message in the input box and press `Ctrl/Cmd+Enter`, or click the checkmark.
- **Sync** — the Status Bar shows `↑2 ↓1` style indicators (commits to push / pull) next to the branch name; clicking it runs a pull-then-push in one step.

Committing directly from the "Changes" section without staging anything first (typing a message and hitting the checkmark) stages and commits **all** changes in one action — a convenient shortcut, but one worth being deliberate about if you only want part of your changes committed.

---

## 6.3 Viewing Diffs And History

Clicking any changed file in the Source Control panel opens a side-by-side diff view, showing the working copy against the last committed version.

```
┌───────────────┬───────────────┐
│  HEAD (before)  │  Working Tree  │
├───────────────┼───────────────┤
│ - return a + b; │ + return a * b;│
└───────────────┴───────────────┘
```

For deeper history:

- **Timeline view** (bottom of the Explorer side bar, when a file is selected) — lists every commit that touched the currently open file, newest first, each clickable to view that version's diff.
- **`git log` via Command Palette** — "Git: View File History" or "Git: View History (Git Blame Details)" show a fuller commit log, though the built-in history tooling is intentionally simpler than a dedicated Git client — many developers add the GitLens extension for a richer log graph and inline blame annotations.

---

## 6.4 GitHub/GitLab Integration

VS Code integrates with GitHub particularly deeply, since it's a Microsoft product and GitHub is a Microsoft subsidiary, though GitLab and Bitbucket are well supported through their own extensions.

- **GitHub Pull Requests and Issues extension** — browse, review, and comment on pull requests, and check out a PR's branch locally, all without leaving VS Code.
- **GitHub authentication** — VS Code can sign in with a GitHub account directly (Accounts icon, bottom-left corner) to enable cloning private repos and Settings Sync via a GitHub identity.
- **Publish to GitHub** — a brand-new, not-yet-a-Git-repo folder can be pushed to a new GitHub repository directly from the Source Control panel's "Publish to GitHub" button, skipping the manual `git init`/`git remote add`/`git push` sequence entirely.
- **GitLens** (extension) — adds inline "blame" annotations at the end of each line showing who last changed it and when, plus a richer commit graph and comparison tools.

```
function calculateTotal() {   Jane Doe, 3 days ago • Fix rounding bug
```

This tight integration is a major reason VS Code is often the default choice for open-source contributors — cloning a repo, checking out a PR, and pushing a fix can all happen without ever opening a separate terminal or browser tab.

[Previous](./[5]-Extensions-And-Customization.md) | [Table of Contents](./[0]-Introduction-to-VisualStudioCode.md) | [Next](./[7]-Tasks,-Snippets,-And-Remote-Development.md)
