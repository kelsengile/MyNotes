[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)

# cat (concatenate)

`cat` prints the contents of one or more files to the terminal, and can also join several files together into one. It's one of the very first commands most people learn on Linux or macOS.

Download: [https://man7.org/linux/man-pages/man1/cat.1.html](https://man7.org/linux/man-pages/man1/cat.1.html)

---

## What Is cat?

Despite the name "concatenate," `cat` is used at least as often for its simpler job: quickly dumping a file's contents to the screen. For anything longer than a screenful, `less` (covered separately) is usually the better tool, since `cat` doesn't let you scroll.

---

## Core Commands

```bash
cat file.txt                    # Print a file's contents
cat file1.txt file2.txt          # Print both files, one after another
cat file1.txt file2.txt > combined.txt   # Concatenate into a new file
cat -n file.txt                  # Print with line numbers
cat > notes.txt                  # Type text directly into a new file (Ctrl+D to finish)
```

---

## A Quick Way to Create Files

```bash
cat > notes.txt
This becomes the file's content.
Press Ctrl+D when finished.
```

This is a fast way to create a small file with a few lines of text without opening a text editor at all.

---

## Example Walkthrough

```bash
cat chapter1.txt chapter2.txt chapter3.txt > full_book.txt
cat -n full_book.txt | head -20
```

Joins three chapter files into one, then previews the first 20 numbered lines of the combined result.

[⬅ Back to Command Fundamentals](../[0]-Introduction-to-Command.md)
