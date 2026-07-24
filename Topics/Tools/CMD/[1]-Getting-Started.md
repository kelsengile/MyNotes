# Lesson 1: Getting Started with CMD

## Opening Command Prompt

Any of these will work:
- Press `Win + R`, type `cmd`, press Enter.
- Search "Command Prompt" in the Start menu.
- Type `cmd` into the address bar of File Explorer, then press Enter (opens CMD in that folder).

## Reading the prompt

When CMD opens you'll see something like:

```
C:\Users\YourName>
```

This tells you two things: the drive (`C:`) and the current folder (`\Users\YourName`). Whatever you type appears after the `>`.

## Your first commands

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

## Getting help

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

## Clearing the screen

```
cls
```
Clears all the clutter so you can start fresh.

## Exiting CMD

```
exit
```
Closes the window.

## Try it yourself

1. Open CMD and run `ver`, `date`, and `time`.
2. Run `help` and scroll through the list — don't worry about understanding every command yet.
3. Run `dir /?` and see how much detail is packed into one help page.


