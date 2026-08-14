[Previous](./[13]-Batch-Scripting-Control-Flow.md) | [Table of Contents](./[0]-Introduction.md)

# Lesson 14 - Tips, Tricks, and Shortcuts

## 14.1 Command history

- **Up / Down arrows** — cycle through previously typed commands.
- `doskey /history` — print your full command history for this session.
- **F7** — opens a popup list of your command history you can navigate with arrow keys.

---

## 14.2 Autocomplete

Start typing a file or folder name and press **Tab** — CMD will try to complete it for you. Press **Tab** again to cycle through other matches.

---

## 14.3 Repeating or reusing part of a previous command

- **F3** — retypes your last command in full.
- Typing a few characters and pressing **F8** cycles through past commands that started with those characters.

---

## 14.4 Copy and paste

- In modern Windows Terminal / CMD, `Ctrl + C` and `Ctrl + V` work as expected once text is selected.
- In older CMD windows, right-click to paste, and click-and-drag to select text to copy (then press Enter or right-click to copy).

---

## 14.5 Running multiple commands on one line

```
mkdir test & cd test & echo done
```
The `&` separates commands that all run regardless of whether earlier ones succeeded. Compare with `&&` (only run next if previous succeeded) and `||` (only run next if previous failed), covered in Lesson 13.

---

## 14.6 Clearing clutter

```
cls
```
Clears the screen without affecting anything else.

---

## 14.7 Useful keyboard shortcuts

| Shortcut | Effect |
|----------|--------|
| `Ctrl + C` | Cancel/stop the currently running command |
| `Tab` | Autocomplete file/folder names |
| `F7` | Show command history popup |
| `Up` / `Down` | Cycle through history |
| `Esc` | Clear the current line |

---

## 14.8 Opening CMD in a specific folder quickly

In File Explorer, click the address bar, type `cmd`, and press Enter — CMD opens already positioned in that folder. This saves a lot of `cd` typing.

---

## 14.9 Running a command as a one-off with elevated rights

If you occasionally need one Administrator command without opening a whole new elevated window, you can use:
```
runas /user:Administrator "command here"
```
(You'll be asked for the Administrator account's password.)

---

## 14.10 Where to go from here

You've now covered navigation, file management, redirection, environment variables, system and network diagnostics, disk tools, users, and batch scripting. From here, natural next steps include:
- Practicing by writing small batch scripts for real tasks you do often.
- Learning **PowerShell**, which builds on many of these same ideas with more modern features.
- Exploring `robocopy /?`, `netsh /?`, and other deep commands mentioned only briefly in this course.

---

[Previous](./[13]-Batch-Scripting-Control-Flow.md) | [Table of Contents](./[0]-Introduction.md)
