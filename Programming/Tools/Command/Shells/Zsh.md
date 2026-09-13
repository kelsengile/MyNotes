[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

# Zsh (Z Shell)

Zsh is an interactive shell built on top of the same POSIX foundations as Bash, but with far more built-in customization, smarter tab completion, and a large plugin ecosystem. It's been the default shell on macOS since Catalina (2019).

Download: [https://www.zsh.org/](https://www.zsh.org/)

---

## What Is Zsh?

Zsh is mostly compatible with Bash for everyday commands and even most scripts, but adds quality-of-life features Bash lacks out of the box: smarter globbing, spelling correction, better tab completion (including completing flags and remote paths), and a rich configuration system through `.zshrc`.

---

## Everyday Commands

```zsh
ls -la                        # Same core commands as Bash work in Zsh
cd -                          # Jump back to the previous directory
setopt AUTO_CD                # Type a folder name alone to cd into it
alias ll='ls -la'             # Define a shortcut command
```

---

## Frameworks and Plugins

Most people don't run plain Zsh — they layer a framework on top for prompts, plugins, and themes. The most common is **Oh My Zsh**, which bundles hundreds of optional plugins (git shortcuts, syntax highlighting, autosuggestions) and manages them through `~/.zshrc`.

```zsh
# ~/.zshrc snippet
plugins=(git zsh-autosuggestions)
ZSH_THEME="robbyrussell"
```

---

## Example Walkthrough

```zsh
alias gs='git status'
setopt AUTO_CD
Documents
```

Defines a shortcut for `git status`, enables typing a folder name to `cd` into it, then does exactly that — jumping into the `Documents` folder without typing `cd`.

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
