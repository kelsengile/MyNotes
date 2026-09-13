[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)

# tail

`tail` prints the end of a file or input stream — by default, the last 10 lines. It's the go-to command for checking the most recent entries in a log file.

Download: [https://man7.org/linux/man-pages/man1/tail.1.html](https://man7.org/linux/man-pages/man1/tail.1.html)

---

## What Is tail?

Log files grow continuously, and the newest, most relevant information is always at the bottom. `tail` lets you jump straight there instead of scrolling through potentially millions of older lines.

---

## Core Commands

```bash
tail file.txt              # Print the last 10 lines (default)
tail -n 50 file.txt          # Print the last 50 lines
tail -f file.log             # Follow the file, printing new lines as they're written
tail -f -n 100 file.log      # Follow the file, starting from the last 100 lines
```

---

## Following a Live Log

```bash
tail -f /var/log/nginx/access.log
```

`-f` ("follow") keeps `tail` running and prints each new line as it's appended to the file — the standard way to watch a server's logs in real time while debugging an issue as it happens. Press `Ctrl+C` to stop following.

---

## Example Walkthrough

```bash
tail -f -n 50 app.log
```

Shows the last 50 lines of the application log immediately, then keeps the terminal open and prints each new line as the running application writes it — ideal for watching what happens as you reproduce a bug.

[⬅ Back to Command-Line Tools](../[0]-Introduction-to-Command.md)
