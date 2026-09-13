[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

# less

`less` is a terminal pager — a tool for viewing large text files or command output one screen at a time, with the ability to scroll both forward and backward.

Download: [https://man7.org/linux/man-pages/man1/less.1.html](https://man7.org/linux/man-pages/man1/less.1.html)

---

## What Is less?

Where `cat` dumps an entire file at once (scrolling straight off the top of your screen if it's long), `less` loads the file for interactive viewing: you scroll, search, and jump around without ever loading the whole thing into memory at once — which matters for very large files or live log output.

---

## Core Commands

```bash
less file.txt          # Open a file for interactive viewing
```

**Keys inside less:**
```
Space / f     Next page
b             Previous page
/pattern      Search forward for a pattern
n             Jump to the next search match
N             Jump to the previous search match
g             Go to the start of the file
G             Go to the end of the file
q             Quit
```

---

## Why "less" and Not "more"?

`less` is a newer, more capable replacement for an older pager called `more` — the name is a joke on the idea that "less is more." Unlike `more`, `less` lets you scroll backward and doesn't need to read the entire file before you can start viewing it.

---

## Example Walkthrough

```bash
grep "ERROR" server.log | less
```

Filters a log file down to error lines, then pipes the result into `less` so you can scroll through and search the filtered output at your own pace instead of it flying past on screen.

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
