[Previous](./[19]-Embedding-Content.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[21]-Canvas-Element-Basics.md)

*Media*

# Lesson 20 - SVG in HTML

## 20.1 Inline SVG

**SVG** (Scalable Vector Graphics) is an XML-based format for vector images that stays crisp at any size. You can embed SVG markup directly inside your HTML.

```html
<svg width="100" height="100" viewBox="0 0 100 100">
  <circle cx="50" cy="50" r="40" fill="steelblue" />
</svg>
```

Inline SVG has a key advantage: its individual shapes become part of the DOM, so they can be styled with CSS and manipulated with JavaScript, just like any other element.

---

## 20.2 `<img>` and `<object>` for SVG

If you don't need to style or script the SVG's internals, you can reference an external `.svg` file just like any other image:

```html
<img src="icon.svg" alt="Settings icon">
```

`<object>` is another option when you need the SVG to remain scriptable while still being external:

```html
<object data="chart.svg" type="image/svg+xml" width="400" height="300"></object>
```

---

## 20.3 When to Use Each Method

| Method | Use when |
|---|---|
| Inline `<svg>` | You need to style individual parts with CSS or animate them with JS |
| `<img src="...svg">` | The SVG is a static icon or illustration, like a photo |
| `<object>` | You need an external, reusable SVG file that still needs to be interactive |

For simple icons and logos that never change, `<img>` keeps your HTML clean. For interactive graphics — like a chart that highlights on hover — inline SVG is the right choice.

[Previous](./[19]-Embedding-Content.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[21]-Canvas-Element-Basics.md)
