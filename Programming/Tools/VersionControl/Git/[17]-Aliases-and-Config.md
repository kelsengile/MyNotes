[Previous](./[16]-Blame-And-Bisect.md) | [Table of Contents](./[0]-Introduction-to-Git.md) | [Next](./[18]-Pull-Requests.md)

# Lesson 17 - Aliases And Configuration

## 17.1 Configuration Recap

As covered in [Installing Git & First-Time Setup](./[1]-Installation-And-Setup.md), Git config exists at three levels:

```bash
git config --system   # /etc/gitconfig — all users on the machine
git config --global    # ~/.gitconfig — all repos for current user
git config --local      # .git/config — this repo only (default if no flag given)
```

Local overrides global, global overrides system.

---

## 17.2 Viewing and Editing Config

```bash
git config --list                  # all settings, with origin file
git config --list --show-origin
git config user.name                # read one value
git config --global --edit           # open ~/.gitconfig directly in your editor
```

---

## 17.3 Creating Aliases

Aliases are shortcuts for commands you type often, defined under `[alias]` in a config file.

```bash
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.st status
git config --global alias.cm "commit -m"
```

Now `git co main` works the same as `git checkout main`.

### Directly Editing `~/.gitconfig`

```ini
[alias]
    co = checkout
    br = branch
    st = status
    cm = commit -m
    unstage = restore --staged
    last = log -1 HEAD
    lg = log --oneline --graph --all --decorate
    amend = commit --amend --no-edit
```

### More Advanced Aliases (Shell Commands)

Prefixing with `!` lets an alias run any shell command, not just Git subcommands:

```ini
[alias]
    undo = "!git reset --soft HEAD~1"
    cleanup = "!git branch --merged | grep -v '\\*\\|main\\|develop' | xargs -n 1 git branch -d"
    save = "!git add -A && git commit -m 'WIP'"
```

---

## 17.4 Useful Config Options Beyond Aliases

### Default push behavior
```bash
git config --global push.default simple
```

### Auto-setup remote tracking on push
```bash
git config --global push.autoSetupRemote true
```

### Rebase instead of merge on pull
```bash
git config --global pull.rebase true
```

### Line ending handling (cross-platform teams)
```bash
git config --global core.autocrlf input    # macOS/Linux
git config --global core.autocrlf true      # Windows
```

### Default branch name for new repos
```bash
git config --global init.defaultBranch main
```

### Colored output
```bash
git config --global color.ui auto
```

### Credential caching (avoid retyping HTTPS passwords)
```bash
git config --global credential.helper cache
git config --global credential.helper 'cache --timeout=3600'

# macOS keychain
git config --global credential.helper osxkeychain

# Windows
git config --global credential.helper manager
```

### Default merge/diff/pager tools

```bash
git config --global core.pager "less -FRX"
git config --global merge.tool vscode
git config --global diff.tool vscode
```

---

## 17.5 Per-Repository Config (Overrides Global)

Useful, for example, if you use a different email for work vs. personal projects:

```bash
cd ~/work/project
git config user.email "you@work-company.com"
```
This writes to `.git/config` for that repo only, overriding the global setting.

### Conditional Includes (Automating Per-Folder Config)

In `~/.gitconfig`:
```ini
[includeIf "gitdir:~/work/"]
    path = ~/.gitconfig-work
```
```ini
# ~/.gitconfig-work
[user]
    email = you@work-company.com
```
Any repo inside `~/work/` automatically picks up the work email.

---

## 17.6 Environment Variables

Git also respects several environment variables that can override config temporarily, e.g.:
```bash
GIT_AUTHOR_NAME="Temp Name" git commit -m "One-off commit"
```

---

## 17.7 Summary

- Config exists at system/global/local levels, with local taking priority.
- Aliases (`git config --global alias.x ...`) turn long or frequent commands into short ones — `!`-prefixed aliases can run full shell commands.
- Common quality-of-life settings: `pull.rebase`, `push.autoSetupRemote`, `core.autocrlf`, `credential.helper`.
- Conditional includes let different folders (e.g. work vs. personal) automatically use different identities.

---

[Previous](./[16]-Blame-And-Bisect.md) | [Table of Contents](./[0]-Introduction-to-Git.md) | [Next](./[18]-Pull-Requests.md)
