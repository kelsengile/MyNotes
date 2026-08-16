[Previous](./[20]-SVG-in-HTML.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[22]-Table-Structure.md)

*Media*

# Lesson 21 - Canvas Element Basics

## 21.1 The `<canvas>` Element

`<canvas>` creates a blank drawing surface on the page. On its own it does nothing — it's a target that JavaScript draws onto, pixel by pixel, rather than describing content declaratively like SVG does.

```html
<canvas id="myCanvas" width="400" height="200"></canvas>
```

Always set `width` and `height` as HTML attributes (not CSS) to avoid a blurry, stretched canvas.

---

## 21.2 Getting a Drawing Context

To actually draw, JavaScript grabs a "context" from the canvas — most commonly the 2D context.

```html
<canvas id="myCanvas" width="400" height="200"></canvas>
<script>
  const canvas = document.getElementById("myCanvas");
  const ctx = canvas.getContext("2d");
</script>
```

The `ctx` object exposes drawing methods: rectangles, paths, text, images, and more.

---

## 21.3 Basic Drawing Example

```html
<canvas id="myCanvas" width="400" height="200"></canvas>
<script>
  const canvas = document.getElementById("myCanvas");
  const ctx = canvas.getContext("2d");

  ctx.fillStyle = "steelblue";
  ctx.fillRect(20, 20, 150, 100);

  ctx.strokeStyle = "black";
  ctx.beginPath();
  ctx.arc(300, 70, 50, 0, Math.PI * 2);
  ctx.stroke();
</script>
```

Canvas is well suited to things like games, data visualizations, and image editing tools that need pixel-level drawing performance — while SVG (Lesson 20) is usually better for static or moderately interactive graphics, since it stays part of the accessible DOM.

[Previous](./[20]-SVG-in-HTML.md) | [Table of Contents](./[0]-Introduction-to-HTML.md) | [Next](./[22]-Table-Structure.md)
