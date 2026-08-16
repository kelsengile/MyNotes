[Previous](./[14]-Figures-Captions-and-Blockquotes.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[16]-Images.md)

*Semantic HTML*

# Lesson 15 - Time, Address & Other Semantic Elements

## 15.1 The `<time>` Element

`<time>` marks a specific date, time, or duration in a way machines can parse, using the `datetime` attribute in a standardized format — while still letting you display the date however you like to humans.

```html
<p>Published on <time datetime="2026-08-16">August 16, 2026</time></p>
<p>The video is <time datetime="PT4M13S">4 minutes 13 seconds</time> long.</p>
```

---

## 15.2 The `<address>` Element

`<address>` wraps contact information for the nearest `<article>` or the whole page — an author's contact details, not just any postal address.

```html
<address>
  Written by <a href="mailto:jane@example.com">Jane Doe</a><br>
  123 Main Street, Springfield
</address>
```

Note: `<address>` is specifically for *contact info*, not for marking up any arbitrary physical address (e.g. a store locator listing addresses shouldn't use it for every entry).

---

## 15.3 Other Useful Semantic Elements

A few more small but meaningful elements worth knowing:

- `<abbr>` — marks an abbreviation, with the full meaning in the `title` attribute.
- `<code>` — marks inline computer code.
- `<kbd>` — marks keyboard input a user should press.
- `<samp>` — marks sample program output.

```html
<p><abbr title="HyperText Markup Language">HTML</abbr> structures web content.</p>
<p>Run <code>npm install</code>, then press <kbd>Enter</kbd>.</p>
<p>Output: <samp>Build succeeded.</samp></p>
```

These small elements add precise meaning that generic text can't — and they often come with useful default browser behavior, like `<abbr>` showing a tooltip on hover.

[Previous](./[14]-Figures-Captions-and-Blockquotes.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[16]-Images.md)
