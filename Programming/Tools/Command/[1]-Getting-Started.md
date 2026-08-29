[Previous](./[0]-Introduction-to-Command.md) | [Table of Contents](./[0]-Introduction-to-Command.md) | [Next](./%5B2%5D-Navigating-the-File-System.md)

# Lesson 1 - Getting Started with CMD

## 1.1 Opening Command Prompt

Any of these will work:
- Press `Win + R`, type `cmd`, press Enter.
- Search "Command Prompt" in the Start menu.
- Type `cmd` into the address bar of File Explorer, then press Enter (opens CMD in that folder).

---

## 1.2 Reading the prompt

When CMD opens you'll see something like:

```
C:\Users\YourName>
```

This tells you two things: the drive (`C:`) and the current folder (`\Users\YourName`). Whatever you type appears after the `>`.

---

## 1.3 Your first commands

Try these one at a time:

```
echo Hello, world!
```
Prints text back to the screen.

```
ver
```
Shows your Windows version.

```
date
```
Shows (and lets you change) the system date.

```
time
```
Shows (and lets you change) the system time.

---

## 1.4 Getting help

CMD has built-in help for almost every command:

```
help
```
Lists all built-in commands with a one-line description.

```
help dir
```
or

```
dir /?
```
Both show detailed help for the `dir` command specifically. The `/?` switch works after **any** command — this is the single most useful trick in CMD.

---

## 1.5 Clearing the screen

```
cls
```
Clears all the clutter so you can start fresh.

---

## 1.6 Exiting CMD

```
exit
```
Closes the window.

---

[Previous](./[0]-Introduction-to-Command.md) | [Table of Contents](./[0]-Introduction-to-Command.md) | [Next](./%5B2%5D-Navigating-the-File-System.md)
