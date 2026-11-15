[⬅ Back to 2D Graphics Fundamentals](./[0]-Introduction-to-2DGraphics.md)

<div align="center">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/illustrator/illustrator-plain.svg" width="50"/><br>
  <h1 style="margin-top: 0;">Adobe Illustrator</h1>
</div>

Adobe Illustrator is the industry-standard vector graphics editor, used to create logos, icons, typography, infographics, and illustrations that stay perfectly sharp at any size. It is paid, subscription-based software available for Windows and macOS (with a free trial).

Website: [https://www.adobe.com/products/illustrator.html](https://www.adobe.com/products/illustrator.html)

---

## What Is Illustrator?

Instead of storing a grid of pixels, Illustrator stores shapes described by math: anchor points, curves, strokes, and fills. Because the shapes are calculated rather than drawn pixel by pixel, a design can be scaled from a favicon to a billboard with no loss of quality. That makes Illustrator the go-to tool whenever artwork has to appear at many different sizes.

It is commonly used for:

- Logos and brand identities
- Icons and icon sets
- Infographics and diagrams
- Typography and lettering
- Print layouts such as posters and packaging

---

## Getting Started

1. Download **Adobe Creative Cloud** from the Adobe website and sign in.
2. Install Illustrator from the Creative Cloud app (a 7-day free trial is available).
3. Choose **File → New** and pick a preset (Web, Print, Mobile) or enter a custom size.
4. Set the color mode: **RGB** for screens, **CMYK** for print.

---

## The Interface

| Area | Purpose |
|---|---|
| **Toolbar** (left) | Selection, Pen, Shape, Type, and other drawing tools |
| **Control bar** (top) | Context-sensitive options for the selected tool or object |
| **Artboard** (center) | The canvas where your design lives |
| **Properties panel** (right) | Quick access to fill, stroke, alignment, and effects |
| **Layers panel** | Organizes and reorders objects |
| **Swatches panel** | Stores colors and gradients for reuse |

---

## Key Concepts

- **Artboards** – separate canvases within one file, useful for icon sets, social media sizes, or multi-page layouts.
- **Paths and anchor points** – every shape is a path made of anchor points joined by straight or curved segments.
- **Pen tool and Bézier curves** – draw precise shapes by placing anchor points and dragging handles to shape curves.
- **Fills and strokes** – the interior color and outline of every object, each controlled independently.
- **Shape Builder and Pathfinder** – combine, subtract, intersect, and divide shapes to build complex forms from simple ones.
- **Swatches and global colors** – change a color once and update it everywhere it's used.
- **Appearance panel** – stack multiple fills, strokes, and effects on a single object non-destructively.

---

## Useful Shortcuts

| Shortcut (Windows / macOS) | Action |
|---|---|
| `V` | Selection tool |
| `A` | Direct Selection tool (edit anchor points) |
| `P` | Pen tool |
| `M` / `L` | Rectangle / Ellipse tool |
| `Shift + M` | Shape Builder tool |
| `Ctrl/Cmd + G` | Group selected objects |
| `Ctrl/Cmd + Shift + O` | Convert text to outlines |
| `Ctrl/Cmd + Z` | Undo |

---

## Raster vs Vector in Illustrator

Illustrator is built for vector work, but it can also place raster images such as photos. Placed images can be **embedded** (stored inside the file) or **linked** (referenced from disk). For logos and print artwork, keep everything as vector wherever possible so the result can be scaled freely.

---

## Example Walkthrough

Building a simple circular logo mark:

1. Create a new document with a single 1000 × 1000 px artboard.
2. Draw a circle with the **Ellipse tool** (`L`) while holding `Shift` to keep it perfect.
3. Draw a smaller rectangle over it with the **Rectangle tool** (`M`).
4. Select both shapes and use the **Shape Builder tool** (`Shift + M`) to subtract the rectangle from the circle.
5. Apply a fill color from the **Swatches** panel and remove the stroke.
6. Add a text layer with the **Type tool** (`T`) and convert it to outlines with `Ctrl/Cmd + Shift + O`.
7. Export with **File → Export → Export As** as SVG for web use, or **Save As** PDF for print.

---

## Tips and Common Mistakes

- **Name your layers.** Files with dozens of "Layer 1" entries become impossible to manage.
- **Use global swatches.** Rebranding a color then takes a single click.
- **Convert text to outlines before sending files** to a printer or client, so a missing font can't break the design.
- **Don't scale strokes by accident.** Check *Scale Strokes & Effects* in preferences when resizing.
- **Save a working `.ai` file** in addition to any exported formats.

---

## When To Choose Illustrator

Choose it when you need scalable artwork, precise shapes, or professional print output. If you only edit photos, a raster editor like [GIMP](./Gimp.md) is a better fit; if you mainly paint, look at [Krita](./Krita.md). Free vector alternatives exist, such as Inkscape, if you want to avoid a subscription.

---

## Where To Go Next

- Recreate a logo you like using only basic shapes and the Pathfinder tools.
- Practice the Pen tool until curves feel natural; it is the most important skill in vector work.
- Build a small icon set on a shared grid for consistent sizing.

[⬅ Back to 2D Graphics Fundamentals](./[0]-Introduction-to-2DGraphics.md)