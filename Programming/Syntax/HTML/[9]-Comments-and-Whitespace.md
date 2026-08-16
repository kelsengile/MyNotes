[Previous](./[8]-Links-and-Anchors.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[10]-Special-Characters-and-Entities.md)

*Text & Content Basics*

# Lesson 9 - Comments & Whitespace Handling

## 9.1 HTML Comments

Comments let you leave notes in your code that the browser ignores completely — they're never shown on the page.

```html
<!-- This is a comment -->
<p>Visible text</p>
<!-- 
  Comments can also
  span multiple lines
-->
```

Comments are useful for explaining tricky markup, temporarily disabling a block of HTML, or leaving TODOs for collaborators.

---

## 9.2 Whitespace Collapsing

Browsers **collapse** whitespace by default: multiple spaces, tabs, and line breaks in your source code are rendered as a single space.

```html
<p>This      has     lots   of
spaces and
line breaks.</p>
```

This renders as: "This has lots of spaces and line breaks." — all the extra whitespace collapses down to single spaces. This is why indenting your HTML for readability never affects how it looks in the browser.

---

## 9.3 Preformatted Text

If you need to preserve whitespace exactly as written — for code snippets, ASCII art, or anything with meaningful spacing — use the `<pre>` element.

```html
<pre>
function greet() {
  console.log("Hello!");
}
</pre>
```

Text inside `<pre>` keeps its exact spacing and line breaks. It's commonly paired with `<code>` to display formatted code samples:

```html
<pre><code>const x = 5;
const y = 10;</code></pre>
```

[Previous](./[8]-Links-and-Anchors.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[10]-Special-Characters-and-Entities.md)
