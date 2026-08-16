[Previous](./[39]-Keyboard-Navigation-and-Focus.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[41]-Details-and-Summary.md)

*Accessibility (a11y)*

# Lesson 40 - Accessible Forms, Images & Media

## 40.1 Accessible Forms Recap

Accessible forms depend on features already covered in Lessons 25–31, applied consistently:

- Every input has a real `<label>` (Lesson 27) — never rely on `placeholder` alone.
- `<fieldset>` and `<legend>` (Lesson 27) group related controls, like radio buttons, with context.
- `required` and other validation attributes (Lesson 30) communicate constraints natively, so assistive technology can announce them.

```html
<label for="email">Email address</label>
<input type="email" id="email" name="email" required aria-describedby="email-hint">
<p id="email-hint">We'll never share your email.</p>
```

`aria-describedby` links extra helper text to an input, so screen readers announce it alongside the label.

---

## 40.2 Accessible Images Recap

From Lesson 16: every meaningful `<img>` needs descriptive `alt` text; purely decorative images use `alt=""` so screen readers skip them. The same principle extends to SVG (Lesson 20) — inline SVG icons should include a `<title>` element or `aria-label`, and decorative SVGs should carry `aria-hidden="true"`.

```html
<svg role="img" aria-label="Warning icon">
  <title>Warning</title>
  <path d="..."></path>
</svg>
```

---

## 40.3 Accessible Media Recap

From Lesson 18: `<video>` should include a `<track kind="captions">` for anyone who is deaf, hard-of-hearing, or simply watching without sound. Audio-only content benefits from a text transcript linked nearby.

```html
<video controls>
  <source src="talk.mp4" type="video/mp4">
  <track src="captions-en.vtt" kind="captions" srclang="en" label="English" default>
</video>
<p><a href="transcript.html">Read the full transcript</a></p>
```

Across forms, images, and media, the same theme holds: accessibility is rarely a separate add-on step — it's the natural result of using the semantic elements and attributes this course has covered all along, applied consistently and with the user's real experience in mind.

[Previous](./[39]-Keyboard-Navigation-and-Focus.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[41]-Details-and-Summary.md)
