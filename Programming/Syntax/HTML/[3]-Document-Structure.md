[Previous](./[2]-Setting-Up-Your-Environment.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[4]-Elements-Tags-and-Attributes.md)

*Getting Started*

# Lesson 3 - Document Structure

## 3.1 The DOCTYPE Declaration

Every HTML document should begin with a **DOCTYPE** declaration. It tells the browser which version of HTML the page uses so it renders in "standards mode" instead of a quirky, legacy compatibility mode.

```html
<!DOCTYPE html>
```

This single line is the modern DOCTYPE for HTML5, and it's the only DOCTYPE you'll need going forward.

---

## 3.2 The `<html>` Element

The `<html>` element wraps the entire page — everything else lives inside it. It's good practice to declare the page's language using the `lang` attribute (more on this in Lesson 47).

```html
<html lang="en">
  ...
</html>
```

---

## 3.3 The `<head>` Element

The `<head>` contains **metadata** — information about the page that isn't displayed directly on screen. This includes the page title, character encoding, linked stylesheets, and more.

```html
<head>
  <meta charset="UTF-8">
  <title>Page Title</title>
</head>
```

We'll cover the `<head>` in much more detail in Lesson 32.

---

## 3.4 The `<body>` Element

The `<body>` contains everything the user actually sees: text, images, links, forms, and so on. Everything covered from here through Lesson 46 lives inside the `<body>`.

```html
<body>
  <h1>Welcome</h1>
  <p>This content is visible on the page.</p>
</body>
```

Putting it all together:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8">
    <title>My Page</title>
  </head>
  <body>
    <h1>Welcome</h1>
  </body>
</html>
```

This is the minimum skeleton every HTML document should have.

[Previous](./[2]-Setting-Up-Your-Environment.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[4]-Elements-Tags-and-Attributes.md)
