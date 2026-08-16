[Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[2]-Setting-Up-Your-Environment.md)

*Getting Started*

# Lesson 1 - What is HTML & How the Web Works

## 1.1 What Does HTML Stand For?

HTML stands for **HyperText Markup Language**. It isn't a programming language — it doesn't have loops or logic. Instead, it's a *markup* language: a way of wrapping content in tags that describe what that content **is** (a heading, a paragraph, a list, an image) rather than how it looks.

```html
<h1>This is a heading</h1>
<p>This is a paragraph of text.</p>
```

The browser reads these tags and knows how to display each piece of content appropriately.

---

## 1.2 How Browsers Render HTML

When you open a web page, your browser:

1. Downloads the HTML file from a server.
2. Parses the HTML into a tree-like structure called the **DOM** (Document Object Model).
3. Downloads any linked resources (CSS, images, fonts, JavaScript).
4. Applies CSS to style the DOM, then paints pixels on the screen.
5. Runs any JavaScript, which can further change the page.

HTML is the *structure* — CSS is the *presentation*, and JavaScript is the *behavior*. This course focuses entirely on the first piece: structure.

---

## 1.3 Client-Server Basics

The web works on a **client-server** model:

- Your browser (the **client**) sends a request for a page, usually by URL.
- A **server** somewhere receives that request and sends back an HTML file (plus other assets).
- The browser renders what it receives.

```
Browser  --- requests page --->  Server
Browser  <--- sends HTML file ---  Server
```

You don't need to understand servers deeply to write HTML — but knowing that HTML is just a text file your browser interprets makes everything else in this course easier to follow.

[Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[2]-Setting-Up-Your-Environment.md)
