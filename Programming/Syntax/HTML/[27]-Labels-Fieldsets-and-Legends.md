[Previous](./[26]-Input-Types.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[28]-Select-Option-and-Datalist.md)

*Forms*

# Lesson 27 - Labels, Fieldsets & Legends

## 27.1 The `<label>` Element

`<label>` associates descriptive text with a form control. Connect them using a matching `for`/`id` pair, or by wrapping the input directly inside the label.

```html
<!-- Method 1: for/id pair -->
<label for="email">Email address</label>
<input type="email" id="email" name="email">

<!-- Method 2: wrapping -->
<label>
  Email address
  <input type="email" name="email">
</label>
```

Labels aren't just visual — clicking a label focuses (or toggles) its associated input, and screen readers announce the label whenever the input receives focus. Never rely on placeholder text alone as a substitute for a real label.

---

## 27.2 `<fieldset>` and `<legend>`

`<fieldset>` groups related controls together, and `<legend>` provides a caption for that group — commonly used for radio button groups or sections of a longer form.

```html
<fieldset>
  <legend>Preferred Size</legend>
  <label><input type="radio" name="size" value="small"> Small</label>
  <label><input type="radio" name="size" value="medium"> Medium</label>
  <label><input type="radio" name="size" value="large"> Large</label>
</fieldset>
```

Screen readers announce the legend before each control in the group, so users understand what "Small," "Medium," and "Large" actually refer to — context that would otherwise be lost.

`<fieldset>` also supports a `disabled` attribute, which disables every control inside it at once:

```html
<fieldset disabled>
  <legend>Shipping (unavailable)</legend>
  <input type="text" name="address">
</fieldset>
```

[Previous](./[26]-Input-Types.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[28]-Select-Option-and-Datalist.md)
