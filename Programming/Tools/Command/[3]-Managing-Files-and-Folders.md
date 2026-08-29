[Previous](./%5B2%5D-Navigating-the-File-System.md) | [Table of Contents](./[0]-Introduction-to-Command.md) | [Next](./%5B4%5D-Viewing-and-Creating-File-Content.md)

# Lesson 3 - Managing Files and Folders

## 3.1 Creating folders

```
mkdir Projects
```
or the shorter alias:
```
md Projects
```

---

## 3.2 Removing folders

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

---

## 3.3 Copying files

```
copy report.txt backup\report.txt
```
Copies `report.txt` into the `backup` folder.

```
copy *.txt backup\
```
Copies every `.txt` file in the current folder into `backup`.

---

## 3.4 Copying whole folders

`copy` doesn't handle folders well. Use `xcopy` or `robocopy` instead:

```
xcopy Projects Projects-Backup /e /i
```
`/e` includes empty subfolders, `/i` assumes the destination is a folder.

```
robocopy Projects Projects-Backup /e
```
`robocopy` ("robust copy") is the modern, more reliable tool for copying folder trees — it can resume interrupted copies and log what it did.

---

## 3.5 Moving files

```
move report.txt archive\
```
Same syntax as `copy`, but the original is removed after the move. `move` also works for renaming.

---

## 3.6 Renaming files

```
ren oldname.txt newname.txt
```

---

## 3.7 Deleting files

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

---

[Previous](./%5B2%5D-Navigating-the-File-System.md) | [Table of Contents](./[0]-Introduction-to-Command.md) | [Next](./%5B4%5D-Viewing-and-Creating-File-Content.md)
