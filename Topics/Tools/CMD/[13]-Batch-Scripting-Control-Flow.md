# Lesson 13: Batch Scripting — Control Flow

## Conditional logic with `if`

```bat
@echo off
set /p answer=Do you want to continue? (y/n) 
if %answer%==y (
    echo Continuing...
) else (
    echo Stopping.
)
```

Checking whether a file exists:
```bat
if exist notes.txt (
    echo Found it!
) else (
    echo File not found.
)
```

Checking whether a folder exists:
```bat
if exist "C:\MyFolder\" (
    echo Folder exists.
)
```

## Chaining commands with `&&` and `||`

- `command1 && command2` — run `command2` only if `command1` succeeded.
- `command1 || command2` — run `command2` only if `command1` failed.

```bat
ping -n 1 google.com >nul && echo Internet is up || echo Internet is down
```

## Loops with `for`

Loop over files:
```bat
for %%f in (*.txt) do echo Found file: %%f
```
(Note the double `%%` — inside a `.bat` file, variables from `for` need two percent signs. In a single command typed directly into CMD, you'd use one: `for %f in (*.txt) do echo %f`.)

Loop a fixed number of times:
```bat
for /l %%i in (1,1,5) do echo Number: %%i
```
This means "start at 1, step by 1, stop at 5."

Loop over lines in a file:
```bat
for /f "tokens=*" %%line in (names.txt) do echo Name: %%line
```

## Labels and `goto`

```bat
@echo off
set count=0

:loop
set /a count+=1
echo Count is %count%
if %count% lss 5 goto loop

echo Done!
```
`:loop` is a label — a marker you can jump back to. `goto loop` jumps to it. `set /a` performs arithmetic (plain `set` treats everything as text).

## Putting it together: a simple menu script

```bat
@echo off
:menu
cls
echo 1. Show date
echo 2. Show time
echo 3. Exit
set /p choice=Choose an option: 

if %choice%==1 (date /t & pause & goto menu)
if %choice%==2 (time /t & pause & goto menu)
if %choice%==3 exit
goto menu
```

## Try it yourself

1. Write a script that checks if a file called `data.txt` exists, and creates it with `echo` if it doesn't.
2. Write a `for /l` loop that counts from 1 to 10.
3. Build the menu script above and add a fourth option of your choosing.

