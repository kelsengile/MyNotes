[Previous](./[29]-Textareas-Buttons-and-Form-Controls.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[31]-File-Uploads.md)

*Forms*

# Lesson 30 - Form Validation Attributes

## 30.1 `required`

`required` prevents the form from submitting until the field has a value.

```html
<label for="email">Email (required)</label>
<input type="email" id="email" name="email" required>
```

The browser blocks submission and shows a built-in error message if a required field is left empty.

---

## 30.2 `pattern`

`pattern` validates the input against a regular expression, useful for formats built-in types don't cover, like a specific ID format.

```html
<label for="zip">ZIP Code</label>
<input type="text" id="zip" name="zip" pattern="[0-9]{5}" title="Enter a 5-digit ZIP code">
```

The `title` attribute is shown to the user as guidance if the pattern doesn't match.

---

## 30.3 `min`, `max`, and `step`

For numeric and date-related inputs, `min` and `max` set boundaries, and `step` controls the increment allowed.

```html
<label for="qty">Quantity</label>
<input type="number" id="qty" name="qty" min="1" max="20" step="1">

<label for="event-date">Event Date</label>
<input type="date" id="event-date" name="event-date" min="2026-01-01" max="2026-12-31">
```

There's also `minlength` and `maxlength` for constraining the number of characters in text fields.

---

## 30.4 Custom Validation Messages

You can override the browser's default validation message using JavaScript's `setCustomValidity()` method:

```html
<input type="text" id="username" name="username" required
  oninput="this.setCustomValidity(this.value.includes(' ') ? 'No spaces allowed' : '')">
```

Built-in validation attributes cover the majority of everyday cases without any JavaScript at all — reach for custom validation only when a rule can't be expressed with `required`, `pattern`, or the numeric/length constraints above.

[Previous](./[29]-Textareas-Buttons-and-Form-Controls.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[31]-File-Uploads.md)
