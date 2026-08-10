# Lesson 6: Environment Variables and PATH

## What's an environment variable?

A named value that Windows and programs can read — things like your username, the Windows folder location, or where temporary files go.

## Viewing all environment variables

```
set
```
Lists every environment variable currently defined.

## Viewing one variable

```
echo %USERNAME%
```
Percent signs on either side of the name tell CMD "substitute the value of this variable here." Some common built-in ones:

- `%USERNAME%` — your Windows username
- `%COMPUTERNAME%` — the machine's network name
- `%WINDIR%` — where Windows is installed (usually `C:\Windows`)
- `%TEMP%` — the temporary files folder
- `%PATH%` — the list of folders CMD searches for programs

## Setting a variable for the current session

```
set MYVAR=Hello
echo %MYVAR%
```
This only lasts until you close the CMD window.

## Setting a variable permanently

Use `setx` instead of `set`:
```
setx MYVAR "Hello"
```
This saves it for future CMD sessions (you'll need to open a new window to see it take effect).

## What is PATH?

`PATH` is the most important environment variable for a command-line user. It's a semicolon-separated list of folders. When you type a command name (like `python` or `git`), CMD searches these folders in order looking for a matching program.

View it:
```
echo %PATH%
```

If you install a program and typing its name gives "not recognized," it usually means that program's folder isn't in PATH.

## Adding to PATH permanently

```
setx PATH "%PATH%;C:\MyTools"
```
This appends `C:\MyTools` to your permanent PATH. **Be careful** — a mistake here can break your PATH, so it's often safer to edit PATH through Windows' System Properties → Environment Variables dialog instead.

## Try it yourself

1. Run `echo %USERNAME%` and `echo %COMPUTERNAME%`.
2. Run `set` and see how many variables are defined on your system.
3. Set a temporary variable, `echo` it back, then close and reopen CMD and try again — notice it's gone.
