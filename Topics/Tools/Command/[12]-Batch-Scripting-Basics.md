[Previous](./[11]-User-And-Permissions-Basics.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[13]-Batch-Scripting-Control-Flow.md)

# Lesson 12 - Batch Scripting Basics

A **batch file** (`.bat`) is a plain text file containing a list of CMD commands. Running the file runs the commands in order — this is how you automate repetitive tasks.

## 12.1 Your first batch file

Create a file called `hello.bat` with:

```bat
@echo off
echo Hello, world!
pause
```

- `@echo off` stops CMD from printing each command before running it (just shows the results).
- `echo Hello, world!` prints text.
- `pause` waits for a key press before closing, so the window doesn't vanish immediately when you double-click the file.

Run it either by double-clicking it in File Explorer, or by typing its name in CMD:
```
hello.bat
```

---

## 12.2 Comments

```bat
:: This is a comment and is ignored when the script runs
rem This also works as a comment
```

---

## 12.3 Variables

```bat
@echo off
set name=Alex
echo Hello, %name%!
```

---

## 12.4 Reading input from the user

```bat
@echo off
set /p name=What is your name? 
echo Nice to meet you, %name%!
```
`set /p` prompts the user and stores their typed response in the variable.

---

## 12.5 Using arguments passed to the script

If you run `myscript.bat first second`, inside the script:
- `%1` refers to `first`
- `%2` refers to `second`
- `%0` refers to the script's own name

Example:
```bat
@echo off
echo First argument: %1
echo Second argument: %2
```

---

## 12.6 A simple useful example: a backup script

```bat
@echo off
echo Backing up Documents...
xcopy "%USERPROFILE%\Documents" "D:\Backup\Documents" /e /i /y
echo Done!
pause
```

---

[Previous](./[11]-User-And-Permissions-Basics.md) | [Table of Contents](./[0]-Introduction.md) | [Next](./[13]-Batch-Scripting-Control-Flow.md)
