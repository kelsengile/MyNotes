[⬅ Back to Simulation Fundamentals](./[0]-Introduction-to-Simulations.md)

<div align="center">
  <img src="https://cdn.jsdelivr.net/npm/simple-icons@latest/icons/autodesk.svg" width="50"/><br>
  <h1 style="margin-top: 0;">AutoCAD</h1>
</div>

AutoCAD is a professional computer-aided design (CAD) application from Autodesk for creating precise 2D drawings and 3D models. It is widely used in architecture, engineering, construction, and manufacturing. It is paid software for Windows and macOS, with a free trial and free education licenses for students and educators.

Website: [https://www.autodesk.com/products/autocad/overview](https://www.autodesk.com/products/autocad/overview)

---

## What Is AutoCAD?

AutoCAD focuses on accuracy. Every line has exact coordinates and lengths, so drawings can be used directly for planning, fabrication, and construction. Its native `.dwg` format is a de facto standard in many industries, which makes it easy to exchange files with other professionals.

It is commonly used for:

- Architectural floor plans and elevations
- Mechanical part drawings
- Electrical and plumbing layouts
- Site plans and surveying drawings
- 3D solid modeling and documentation

---

## Getting Started

1. Download AutoCAD (or start a trial) from the Autodesk website and sign in with an Autodesk account.
2. Launch the program and start a new drawing from a template (for example `acad.dwt` for imperial or `acadiso.dwt` for metric).
3. Set drawing units with the `UNITS` command.
4. Turn on helpful drafting aids such as **Object Snap** (`F3`) and **Ortho Mode** (`F8`).

---

## The Interface

| Area | Purpose |
|---|---|
| **Ribbon** (top) | Tabs of tools grouped by task (Home, Insert, Annotate, etc.) |
| **Drawing area** | The canvas where you draw in model space |
| **Command line** (bottom) | Type commands and respond to prompts |
| **Properties palette** | View and edit properties of selected objects |
| **Layer Properties Manager** | Control layer names, colors, linetypes, and visibility |
| **Status bar** | Toggle snaps, grid, ortho, and other drafting aids |
| **Model / Layout tabs** | Switch between drawing space and printable sheets |

---

## Key Concepts

- **Command line** – type commands like `LINE`, `CIRCLE`, or `OFFSET` for fast, precise input.
- **Model space vs paper space** – draw at real-world scale in model space, then arrange views and title blocks in layouts.
- **Layers** – group objects by purpose (walls, dimensions, notes) and control their color, linetype, and visibility.
- **Object snaps (OSNAP)** – lock the cursor to exact points like endpoints, midpoints, and centers.
- **Coordinate input** – enter exact positions using absolute (`x,y`), relative (`@x,y`), or polar (`@distance<angle`) coordinates.
- **Dimensions and annotations** – add measurements, text, and leaders to communicate the design.
- **Blocks** – save common symbols or parts and reuse them throughout a drawing.

---

## Useful Commands

| Command (alias) | Action |
|---|---|
| `LINE` (`L`) | Draw straight lines |
| `CIRCLE` (`C`) | Draw circles |
| `RECTANG` (`REC`) | Draw rectangles |
| `OFFSET` (`O`) | Create parallel copies of objects |
| `TRIM` (`TR`) | Cut objects at intersections |
| `MOVE` (`M`) / `COPY` (`CO`) | Move or duplicate objects |
| `DIMLINEAR` (`DLI`) | Add a linear dimension |
| `EXTRUDE` (`EXT`) | Turn a 2D shape into a 3D solid |

---

## Example Walkthrough

Drafting a simple room plan:

1. Start a new drawing and set units with the `UNITS` command.
2. Create layers named `Walls`, `Doors`, and `Dimensions` in the **Layer Properties Manager**.
3. Make `Walls` the current layer and type `RECTANG`, then enter two corner points to draw the outer room outline.
4. Use `OFFSET` with a distance equal to the wall thickness to create the inner wall line.
5. Cut a door opening with the `TRIM` command and add a door symbol as a block.
6. Switch to the `Dimensions` layer and add measurements with `DIMLINEAR`.
7. Save the drawing as a `.dwg` file.
8. Open a **Layout** tab, insert a title block, set the print scale, and use **Plot** to export a PDF.

---

## Tips and Common Mistakes

- **Draw at real-world size (1:1).** Scale only when plotting.
- **Use layers consistently** to keep drawings organized and easy to edit.
- **Rely on object snaps** instead of clicking by eye, since accuracy is the whole point.
- **Save often** and keep backups; `.bak` files can rescue a corrupted drawing.
- **Check units** before sharing a file, because mixing metric and imperial causes expensive errors.

---

## When To Choose AutoCAD

Choose AutoCAD when you need exact 2D drafting or documentation that can be shared with architects, engineers, and fabricators. For organic or artistic 3D models, [Blender](../3D%20Modeling/Blender.md) is a better fit, and for UI layouts, use [Figma](../Interfaces/Figma.md).

---

## Where To Go Next

- Redraw a simple floor plan you know to practice layers and dimensions.
- Learn blocks and dynamic blocks to speed up repetitive drafting.
- Try extruding a 2D profile into a 3D solid to see how drawings become models.

[⬅ Back to Simulation Fundamentals](./[0]-Introduction-to-Simulations.md)