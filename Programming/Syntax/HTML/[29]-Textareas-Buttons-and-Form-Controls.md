[Previous](./[28]-Select-Option-and-Datalist.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[30]-Form-Validation-Attributes.md)

*Forms*

# Lesson 29 - Textareas, Buttons & Form Controls

## 29.1 `<textarea>`

`<textarea>` creates a multi-line text input, unlike `<input type="text">` which is single-line. Its content goes between the opening and closing tags, not in a `value` attribute.

```html
<label for="comments">Comments</label>
<textarea id="comments" name="comments" rows="4" cols="40">
Default text goes here.
</textarea>
```

`rows` and `cols` suggest a starting size; CSS `resize` can control whether users can drag it larger.

---

## 29.2 The `<button>` Element

`<button>` creates a clickable button, with three possible `type` values:

```html
<button type="submit">Submit the form</button>
<button type="reset">Clear all fields</button>
<button type="button">Do nothing on its own</button>
```

- `submit` (the default inside a form) submits the form.
- `reset` clears all fields back to their default values.
- `button` does nothing by itself — it's meant to be paired with JavaScript.

Always set an explicit `type` — relying on the default can cause unexpected form submissions if a button is later moved or a form's structure changes.

---

## 29.3 Other Form Controls

- `<output>` displays the result of a calculation, often updated via JavaScript.
- `<progress>` shows completion progress of a task.
- `<meter>` shows a scalar value within a known range, like disk usage.

```html
<output name="result" for="a b">42</output>
<progress value="70" max="100"></progress>
<meter value="6" min="0" max="10">6 out of 10</meter>
```

These are less commonly used than text inputs and buttons, but they're the semantically correct choice whenever you'd otherwise reach for a styled `<div>` to represent a progress bar or computed value.

[Previous](./[28]-Select-Option-and-Datalist.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[30]-Form-Validation-Attributes.md)
