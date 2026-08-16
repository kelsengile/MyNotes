[Previous](./[24]-Colspan-and-Rowspan.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[26]-Input-Types.md)

*Forms*

# Lesson 25 - Form Basics

## 25.1 The `<form>` Element

`<form>` wraps a group of controls that collect user input and submit it somewhere.

```html
<form action="/submit" method="post">
  <input type="text" name="username">
  <button type="submit">Submit</button>
</form>
```

---

## 25.2 The `action` Attribute

`action` specifies the URL the form data is sent to when submitted. If omitted, the form submits to the current page's URL.

```html
<form action="/login">
  ...
</form>
```

---

## 25.3 The `method` Attribute

`method` specifies how the data is sent:

- **`get`** (the default) appends form data to the URL as a query string — visible, bookmarkable, and appropriate for searches or filters, but not for sensitive data.
- **`post`** sends form data in the request body — not visible in the URL, and the right choice for anything that changes data or includes sensitive information (like passwords).

```html
<form action="/search" method="get">
  <input type="text" name="q">
  <button type="submit">Search</button>
</form>

<form action="/login" method="post">
  <input type="password" name="password">
  <button type="submit">Log In</button>
</form>
```

Every input intended to be submitted needs a `name` attribute — without it, the browser won't include that field's value in the submitted data. The upcoming lessons cover the many kinds of inputs, labels, and validation you can add inside a form.

[Previous](./[24]-Colspan-and-Rowspan.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[26]-Input-Types.md)
