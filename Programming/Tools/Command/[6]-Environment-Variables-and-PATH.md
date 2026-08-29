[Previous](./%5B5%5D-Redirection-and-Piping.md) | [Table of Contents](./[0]-Introduction-to-Command.md) | [Next](./[7]-System-Information-Commands.md)

# Lesson 6 - Environment Variables and PATH

## 6.1 What's an environment variable?

A named value that Windows and programs can read — things like your username, the Windows folder location, or where temporary files go.

---

## 6.2 Viewing all environment variables

```
set
```
Lists every environment variable currently defined.

---

## 6.3 Viewing one variable

```
echo %USERNAME%
```
Percent signs on either side of the name tell CMD "substitute the value of this variable here." Some common built-in ones:

- `%USERNAME%` — your Windows username
- `%COMPUTERNAME%` — the machine's network name
- `%WINDIR%` — where Windows is installed (usually `C:\Windows`)
- `%TEMP%` — the temporary files folder
- `%PATH%` — the list of folders CMD searches for programs

---

## 6.4 Setting a variable for the current session

```
set MYVAR=Hello
echo %MYVAR%
```
This only lasts until you close the CMD window.

---

## 6.5 Setting a variable permanently

Use `setx` instead of `set`:
```
setx MYVAR "Hello"
```
This saves it for future CMD sessions (you'll need to open a new window to see it take effect).

---

## 6.6 What is PATH?

`PATH` is the most important environment variable for a command-line user. It's a semicolon-separated list of folders. When you type a command name (like `python` or `git`), CMD searches these folders in order looking for a matching program.

View it:
```
echo %PATH%
```

If you install a program and typing its name gives "not recognized," it usually means that program's folder isn't in PATH.

---

## 6.7 Adding to PATH permanently

```
setx PATH "%PATH%;C:\MyTools"
```
This appends `C:\MyTools` to your permanent PATH. **Be careful** — a mistake here can break your PATH, so it's often safer to edit PATH through Windows' System Properties → Environment Variables dialog instead.

---

[Previous](./%5B5%5D-Redirection-and-Piping.md) | [Table of Contents](./[0]-Introduction-to-Command.md) | [Next](./[7]-System-Information-Commands.md)
