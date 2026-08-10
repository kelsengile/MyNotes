# Lesson 2: Navigating the File System

## Checking where you are

```
cd
```
Prints the current directory (folder).

## Listing what's in a folder

```
dir
```
Lists files and subfolders in the current directory, with sizes and dates.

Useful switches:
- `dir /a` — show hidden and system files too
- `dir /s` — list contents of all subfolders as well
- `dir /b` — "bare" format, just names, good for scripting

## Changing directories

```
cd Documents
```
Moves into the `Documents` subfolder of the current folder.

```
cd ..
```
Moves **up** one level (to the parent folder).

```
cd \
```
Jumps straight to the root of the current drive.

```
cd \Users\YourName\Downloads
```
An **absolute path** — jumps directly there regardless of where you currently are.

## Switching drives

CMD treats each drive letter as its own workspace. Typing `cd D:` while on `C:` does *not* switch you to D: — it just remembers a path for later. To actually switch drives, type the drive letter and a colon by itself:

```
D:
```

## Path basics

- Paths use backslashes: `C:\Users\YourName\Documents`
- If a folder name has spaces, wrap it in quotes: `cd "Program Files"`
- `.` means "current folder", `..` means "parent folder"

## Try it yourself

1. Run `cd` to see your starting location.
2. Run `dir` to see what's there.
3. Navigate into a subfolder, then use `cd ..` to go back.
4. Try `dir /b` and compare the output to a plain `dir`.
