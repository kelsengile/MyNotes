[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[3]-How-JavaScript-Works.md)

*Getting Started*

# Lesson 2 - Running JavaScript Code

## 2.1 The Browser Console

Every modern browser ships with a **DevTools console** where you can type and run JavaScript instantly.

- **Chrome/Edge:** press `F12` or `Ctrl+Shift+J` (`Cmd+Option+J` on macOS), then click the "Console" tab.
- **Firefox:** press `F12`, then click "Console".

Try typing `2 + 2` and pressing Enter — the console evaluates it and prints `4`. The console is tied to whatever web page is open, so it's most useful for experimenting with a page's live DOM (covered later in this course) or quickly testing a snippet of code.

---

## 2.2 The Node REPL

Node.js has its own interactive prompt called the **REPL** (Read-Eval-Print Loop). Open your terminal and type:

```bash
node
```

You'll see a `>` prompt. Type any JavaScript and press Enter to see the result immediately:

```
> 5 * 3
15
> "hello".toUpperCase()
'HELLO'
```

Type `.exit` or press `Ctrl+D` to leave the REPL. This is a great tool for quick experiments without creating a file.

---

## 2.3 Running Script Files

For anything beyond a one-liner, you'll write code in a `.js` file and run it with Node. Using the `hello.js` file from Lesson 1:

```bash
node hello.js
```

This executes the entire file top to bottom and prints any `console.log()` output to the terminal. This is how you'll run almost every example in this course — save the code to a file, then run it with `node filename.js`.

---

## 2.4 Running Code In A Browser Page

JavaScript can also run by being linked into an HTML page:

```html
<!DOCTYPE html>
<html>
  <body>
    <script src="app.js"></script>
  </body>
</html>
```

Opening this HTML file in a browser runs `app.js` automatically, and `console.log()` output appears in that page's DevTools console rather than your terminal. This is the setup you'll use once the course reaches DOM manipulation and browser APIs.

---

## 2.5 Comments

Before writing more code, it helps to know how to leave notes in it. Comments are ignored by JavaScript entirely:

```js
// A single-line comment

/*
  A multi-line
  comment block
*/
```

Use comments to explain *why* code does something, not to restate the obvious — good code is often more valuable than a comment explaining bad code.

[Previous](./[1]-Installation-and-Setup.md) | [Table of Contents](./[0]-Introduction-to-JavaScript.md) | [Next](./[3]-How-JavaScript-Works.md)
