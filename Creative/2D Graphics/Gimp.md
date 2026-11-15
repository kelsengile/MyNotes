[⬅ Back to 2D Graphics Fundamentals](./[0]-Introduction-to-2DGraphics.md)

<div align="center">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/gimp/gimp-original.svg" width="50"/><br>
  <h1 style="margin-top: 0;">GIMP</h1>
</div>

GIMP (GNU Image Manipulation Program) is a free, open-source raster image editor for photo retouching, image composition, and general graphic design. It runs on Windows, macOS, and Linux and is a popular free alternative to Adobe Photoshop.

Website: [https://www.gimp.org/downloads/](https://www.gimp.org/downloads/)

---

## What Is GIMP?

GIMP edits images pixel by pixel, which makes it well suited to photographs, textures, and digital compositing. It is community-developed, completely free, and extensible through plugins and scripts, so it can grow with your needs.

It is commonly used for:

- Photo retouching and color correction
- Combining multiple images into one composition
- Resizing, cropping, and preparing images for the web
- Creating banners, thumbnails, and simple graphics
- Texture and pattern creation

---

## Getting Started

1. Download the installer for your operating system from the GIMP website.
2. Run the installer and launch GIMP.
3. Open an existing image with **File → Open**, or create a blank canvas with **File → New**.
4. Optionally switch to single-window mode via **Windows → Single-Window Mode** for a tidier workspace.

---

## The Interface

| Area | Purpose |
|---|---|
| **Toolbox** (left) | Selection, paint, transform, and text tools |
| **Tool Options** (below toolbox) | Settings for the active tool, such as brush size |
| **Canvas** (center) | The image you're editing |
| **Layers panel** (right) | Stack, reorder, and hide image layers |
| **Brushes / Patterns panel** | Choose brush shapes, gradients, and fills |
| **Menu bar** (top) | Filters, color adjustments, and file operations |

---

## Key Concepts

- **Layers** – stack image elements so each can be edited independently without touching the others.
- **Selection tools** – isolate part of an image using rectangles, ellipses, free selection, fuzzy select, or select-by-color.
- **Layer masks** – hide or reveal parts of a layer non-destructively using black and white painting.
- **Channels** – separate color information (red, green, blue, alpha) that can be edited individually.
- **Filters** – apply blurs, sharpening, noise, distortions, and artistic effects.
- **Paths** – vector-style outlines that can be converted to selections.

---

## Useful Shortcuts

| Shortcut | Action |
|---|---|
| `R` | Rectangle select |
| `F` | Free select (lasso) |
| `U` | Fuzzy select (magic wand) |
| `P` | Paintbrush |
| `Shift + E` | Eraser |
| `Ctrl + Shift + N` | New layer |
| `Ctrl + Shift + D` | Duplicate layer |
| `Ctrl + Z` | Undo |

---

## Native Format vs Export

GIMP saves editable projects as **`.xcf`**, which keeps layers, masks, and paths. Formats like JPEG and PNG flatten the image, so always keep the `.xcf` as your master copy and use **File → Export As** to produce share-ready files.

---

## Example Walkthrough

Retouching and exporting a photo for the web:

1. Open the photo with **File → Open**.
2. Duplicate the background layer (`Ctrl + Shift + D`) so the original stays untouched.
3. Adjust tone under **Colors → Curves** or **Colors → Brightness-Contrast**.
4. Use the **Clone tool** (`C`) to remove small blemishes or distractions.
5. Crop with the **Crop tool** (`Shift + C`).
6. Resize with **Image → Scale Image**, setting the width to around 1600 px for web use.
7. Save the editable version as `.xcf`.
8. Create the final copy with **File → Export As** and choose JPEG (photos) or PNG (graphics).

---

## Tips and Common Mistakes

- **Work non-destructively.** Duplicate layers and use masks instead of erasing.
- **Check your resolution** before printing: 300 DPI is the usual target.
- **Don't export over your only copy.** Keep the `.xcf` safe.
- **Use layer groups** to keep complex compositions organized.
- **Save often.** GIMP can recover sessions, but a habit of saving is safer.

---

## When To Choose GIMP

Choose GIMP when you need a capable, free editor for photos and pixel-based artwork. For painting with natural-feeling brushes, [Krita](./Krita.md) is often more comfortable, and for scalable logos and icons, [Adobe Illustrator](./AdobeIllustrator.md) is the better fit.

---

## Where To Go Next

- Practice masking by blending two photos into one composition.
- Learn Curves; it is the most powerful color tool in the program.
- Explore plugins and scripts (Script-Fu and Python) to automate repeated tasks.

[⬅ Back to 2D Graphics Fundamentals](./[0]-Introduction-to-2DGraphics.md)