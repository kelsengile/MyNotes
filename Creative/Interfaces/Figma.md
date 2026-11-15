[⬅ Back to Interfaces Fundamentals](./[0]-Introduction-to-Interfaces.md)

<div align="center">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/figma/figma-original.svg" width="50"/><br>
  <h1 style="margin-top: 0;">Figma</h1>
</div>

Figma is a collaborative design tool for creating user interfaces, wireframes, and interactive prototypes. It runs in the browser, with desktop apps for Windows and macOS, and offers a free tier alongside paid plans for teams.

Website: [https://www.figma.com/downloads/](https://www.figma.com/downloads/)

---

## What Is Figma?

Figma lets teams design and review interfaces together in real time. Designers, developers, and stakeholders can all work from the same file without passing versions back and forth, and changes appear instantly for everyone. Because it is browser-based, there is nothing to install to get started.

It is commonly used for:

- Designing website and mobile app screens
- Building wireframes and clickable prototypes
- Maintaining shared design systems and component libraries
- Collaborative brainstorming (via FigJam)
- Handing designs and specs to developers

---

## Getting Started

1. Create a free account at figma.com.
2. Create a new **Design file** from the dashboard.
3. Press `F` to draw your first **Frame** and pick a preset size from the right-hand panel.
4. Optionally install the desktop app for better performance and local font support.

---

## The Interface

| Area | Purpose |
|---|---|
| **Toolbar** (top) | Move, Frame, shape, pen, text, and comment tools |
| **Layers panel** (left) | Hierarchy of every object, frame, and component |
| **Assets panel** (left) | Browse and insert components and libraries |
| **Canvas** (center) | Infinite workspace for your designs |
| **Design panel** (right) | Position, size, fills, strokes, effects, and auto layout |
| **Prototype tab** (right) | Link frames and define interactions |

---

## Key Concepts

- **Frames** – containers that represent screens or sections, with preset device sizes.
- **Auto Layout** – makes elements resize and space themselves automatically, similar to CSS flexbox.
- **Constraints** – control how elements behave when their parent frame is resized.
- **Components and variants** – reusable elements (buttons, inputs, cards) that can have different states such as default, hover, and disabled.
- **Styles and variables** – store colors, typography, and spacing values for consistent reuse.
- **Prototype mode** – link frames together to simulate navigation, overlays, and transitions.
- **Dev Mode** – gives developers measurements, CSS-like properties, and exportable assets.

---

## Useful Shortcuts

| Shortcut (Windows / macOS) | Action |
|---|---|
| `F` | Frame tool |
| `R` / `O` | Rectangle / Ellipse |
| `T` | Text |
| `Ctrl/Cmd + Alt + K` | Create component |
| `Shift + A` | Add auto layout |
| `Ctrl/Cmd + D` | Duplicate |
| `Ctrl/Cmd + G` | Group selection |
| `Shift + Space` or `Ctrl/Cmd + Alt + Space` | Start presenting |

---

## Example Walkthrough

Designing a simple mobile login screen:

1. Create a new design file and add a **Frame** using the iPhone preset.
2. Add a title with the Text tool (`T`) and two rectangles for email and password fields.
3. Add placeholder text inside each field, then select the field and its text and apply **Auto Layout** (`Shift + A`) so padding stays consistent.
4. Create a rounded rectangle with a label for the "Log in" button.
5. Turn the button into a **Component** (`Ctrl/Cmd + Alt + K`) so it can be reused.
6. Duplicate the frame to create a "Home" screen.
7. Switch to the **Prototype** tab, drag a connection from the button to the Home frame, and set the interaction to *Navigate to*.
8. Press the **Present** button to test the flow, then share the link for feedback.

---

## Tips and Common Mistakes

- **Use Auto Layout early.** Retrofitting it later is much harder.
- **Name your layers and frames** so developers can understand the file.
- **Build components first** for repeated elements instead of copying and pasting.
- **Use styles or variables** for colors and text so global changes are quick.
- **Keep pages organized** (for example "Flows," "Components," "Archive") as files grow.

---

## When To Choose Figma

Choose Figma for UI design, prototyping, and team collaboration. For illustration and logo work, a dedicated vector editor like [Adobe Illustrator](../2D%20Graphics/AdobeIllustrator.md) offers more drawing power, and for animated interface concepts you might combine Figma exports with [Adobe After Effects](../Motion%20Graphics/AdobeAfterEffects.md).

---

## Where To Go Next

- Recreate the interface of an app you use daily to learn spacing and hierarchy.
- Build a small design system with buttons, inputs, and cards.
- Explore community files and plugins to speed up repetitive work.

[⬅ Back to Interfaces Fundamentals](./[0]-Introduction-to-Interfaces.md)