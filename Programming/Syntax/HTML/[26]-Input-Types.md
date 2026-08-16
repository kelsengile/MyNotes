[Previous](./[25]-Form-Basics.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[27]-Labels-Fieldsets-and-Legends.md)

*Forms*

# Lesson 26 - Input Types

## 26.1 Text-Based Inputs

The `<input>` element's `type` attribute determines what kind of control it renders and how it validates input.

```html
<input type="text" name="fullname">
<input type="email" name="email">
<input type="password" name="password">
<input type="tel" name="phone">
<input type="url" name="website">
```

`type="email"` and `type="url"` trigger built-in format validation and often show a specialized on-screen keyboard on mobile devices.

---

## 26.2 Numeric and Date Inputs

```html
<input type="number" name="quantity" min="1" max="10">
<input type="date" name="birthday">
<input type="time" name="appointment">
<input type="month" name="expiry">
```

These render native pickers (like a calendar for `date`) and restrict input to valid values, saving you from writing custom validation.

---

## 26.3 Choice Inputs

```html
<!-- Radio: pick exactly one from a group -->
<input type="radio" name="size" value="small" id="small"> <label for="small">Small</label>
<input type="radio" name="size" value="large" id="large"> <label for="large">Large</label>

<!-- Checkbox: pick any number, independently -->
<input type="checkbox" name="toppings" value="cheese" id="cheese"> <label for="cheese">Cheese</label>
```

Radio buttons sharing the same `name` form a mutually exclusive group; checkboxes act independently even when they share a name.

---

## 26.4 `color` and `range`

```html
<input type="color" name="favcolor" value="#3366ff">
<input type="range" name="volume" min="0" max="100" value="50">
```

`type="color"` opens a native color picker, and `type="range"` renders a slider — both give users a friendlier control than typing raw values.

[Previous](./[25]-Form-Basics.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[27]-Labels-Fieldsets-and-Legends.md)
