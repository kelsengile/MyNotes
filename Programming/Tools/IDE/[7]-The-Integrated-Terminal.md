[Previous](./[6]-Build-Systems-And-Task-Runners.md) | [Table of Contents](./[0]-Introduction-to-IDEs.md) | [Next](./[8]-Introduction-To-Debugging.md)

*Building And Running Code*

# Lesson 7 - The Integrated Terminal

## 7.1 What Is an Integrated Terminal

An integrated terminal is a command-line shell embedded directly inside the IDE window, already pointed at the current project's folder. It runs the same shell you'd use outside the IDE (such as Bash, Zsh, or PowerShell), but saves you from switching to a separate terminal application and manually navigating to the right directory every time.

---

## 7.2 Running Commands

Because the integrated terminal is a real shell, anything you could type in a standalone terminal works here too — installing dependencies, running scripts, or using command-line tools like Git directly. This is often faster than clicking through IDE menus once you're comfortable with the underlying commands, and it's essential for tools that don't have a dedicated IDE integration.

---

## 7.3 Multiple Terminal Instances

IDEs typically let you open several terminal tabs or panes at once — useful for running a development server in one terminal while leaving another free for one-off commands like running tests. Terminals can usually be split side-by-side or stacked, and each keeps its own working directory and command history.

---

## 7.4 Integrated Terminal vs External Shell

An integrated terminal isn't functionally different from an external one — it runs the same shell — but it inherits the IDE's environment variables and can be scripted as part of IDE tasks. Some workflows still call for an external terminal, such as needing a much larger window, running a long-lived process independent of the IDE, or working on a machine where the IDE isn't installed at all.

[Previous](./[6]-Build-Systems-And-Task-Runners.md) | [Table of Contents](./[0]-Introduction-to-IDEs.md) | [Next](./[8]-Introduction-To-Debugging.md)
