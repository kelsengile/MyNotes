# Lesson 4: Viewing and Creating File Content

## Viewing a text file

```
type notes.txt
```
Dumps the entire file to the screen. Fine for short files.

For long files, pipe it through `more` so you can page through it:
```
type notes.txt | more
```
Press `Space` to go to the next page, `Enter` for the next line, `Q` to quit.

## Creating a file with text in it

```
echo Hello, world! > note.txt
```
The `>` symbol sends the output of `echo` into `note.txt` instead of the screen. If `note.txt` already exists, this **overwrites** it.

To **add** a line without erasing what's already there, use `>>`:
```
echo Another line >> note.txt
```

## Creating an empty file

```
type nul > empty.txt
```
`nul` is a special "nothing" device — this trick creates a zero-byte file.

## Combining several files into one

```
copy file1.txt + file2.txt combined.txt
```
Concatenates `file1.txt` and `file2.txt` into `combined.txt`.

## A quick note on text editors

CMD isn't meant for heavy editing. For anything beyond a line or two, open the file in Notepad straight from CMD:
```
notepad notes.txt
```
If `notes.txt` doesn't exist yet, Notepad will offer to create it.

## Try it yourself

1. Create `note.txt` with one line using `echo ... > note.txt`.
2. Append a second line using `>>`.
3. View the result with `type note.txt`.
4. Open the same file in Notepad directly from CMD.
