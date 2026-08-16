[Previous](./[27]-Labels-Fieldsets-and-Legends.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[29]-Textareas-Buttons-and-Form-Controls.md)

*Forms*

# Lesson 28 - Select, Option & Datalist

## 28.1 `<select>` and `<option>`

`<select>` creates a dropdown menu; each `<option>` inside it is one choice.

```html
<label for="country">Country</label>
<select id="country" name="country">
  <option value="us">United States</option>
  <option value="ca" selected>Canada</option>
  <option value="mx">Mexico</option>
</select>
```

- The `value` attribute is what's actually submitted; the text between the tags is what the user sees.
- `selected` pre-selects a default option.
- Add `multiple` to allow selecting more than one option (usually shown as a scrollable list rather than a dropdown).

---

## 28.2 `<optgroup>`

`<optgroup>` groups related options under a shared, non-selectable label — useful for long lists.

```html
<select name="city">
  <optgroup label="Canada">
    <option value="tor">Toronto</option>
    <option value="van">Vancouver</option>
  </optgroup>
  <optgroup label="Mexico">
    <option value="mex">Mexico City</option>
    <option value="gdl">Guadalajara</option>
  </optgroup>
</select>
```

---

## 28.3 `<datalist>`

`<datalist>` pairs with a regular text `<input>` to offer autocomplete suggestions, while still letting the user type any free-form value.

```html
<label for="browser">Favorite browser</label>
<input list="browsers" id="browser" name="browser">
<datalist id="browsers">
  <option value="Chrome">
  <option value="Firefox">
  <option value="Safari">
  <option value="Edge">
</datalist>
```

The key difference from `<select>`: with `<datalist>`, the suggestions are just hints — the user isn't restricted to picking one of them.

[Previous](./[27]-Labels-Fieldsets-and-Legends.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[29]-Textareas-Buttons-and-Form-Controls.md)
