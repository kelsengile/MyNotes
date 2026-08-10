# Lesson 3: Managing Files and Folders

## Creating folders

```
mkdir Projects
```
or the shorter alias:
```
md Projects
```

## Removing folders

```
rmdir Projects
```
or:
```
rd Projects
```
This only works on **empty** folders. To delete a folder and everything inside it:
```
rmdir /s /q Projects
```
`/s` removes subfolders and files, `/q` skips the "are you sure?" prompts. **Use this carefully — there's no recycle bin from CMD.**

## Copying files

```
copy report.txt backup\report.txt
```
Copies `report.txt` into the `backup` folder.

```
copy *.txt backup\
```
Copies every `.txt` file in the current folder into `backup`.

## Copying whole folders

`copy` doesn't handle folders well. Use `xcopy` or `robocopy` instead:

```
xcopy Projects Projects-Backup /e /i
```
`/e` includes empty subfolders, `/i` assumes the destination is a folder.

```
robocopy Projects Projects-Backup /e
```
`robocopy` ("robust copy") is the modern, more reliable tool for copying folder trees — it can resume interrupted copies and log what it did.

## Moving files

```
move report.txt archive\
```
Same syntax as `copy`, but the original is removed after the move. `move` also works for renaming.

## Renaming files

```
ren oldname.txt newname.txt
```

## Deleting files

```
del report.txt
```

```
del *.tmp
```
Deletes every `.tmp` file in the current folder.

Add `/p` to be prompted for confirmation before each deletion:
```
del /p *.tmp
```

## Try it yourself

1. Make a folder called `test`, then `cd` into it.
2. Create a file: `echo hello > note.txt` (covered fully in Lesson 4).
3. Copy it to `note2.txt`, rename `note2.txt` to `note3.txt`, then delete `note3.txt`.
4. Go back up and remove the `test` folder with `rmdir /s /q test`.

