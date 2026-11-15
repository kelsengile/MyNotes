[⬅ Back to 3D Modeling Fundamentals](./[0]-Introduction-to-3DModeling.md)

<div align="center">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/blender/blender-original.svg" width="50"/><br>
  <h1 style="margin-top: 0;">Blender</h1>
</div>

Blender is a free, open-source 3D creation suite covering modeling, sculpting, texturing, rigging, animation, simulation, rendering, compositing, and video editing. It runs on Windows, macOS, and Linux.

Website: [https://www.blender.org/download/](https://www.blender.org/download/)

---

## What Is Blender?

Blender bundles an entire 3D pipeline into one application. You can model an object, texture it, rig and animate it, simulate cloth or smoke, and render the result without leaving the program. It is developed by the Blender Foundation and a large community, and is used by hobbyists, indie game developers, and professional studios alike.

It is commonly used for:

- Modeling game assets and props
- Character sculpting and animation
- Product and architectural visualization
- Motion graphics and short films
- Preparing models for 3D printing

---

## Getting Started

1. Download Blender from the official website (the LTS version is a stable choice).
2. Launch it and choose the **General** template on the splash screen.
3. Open **Edit → Preferences** to adjust settings such as keymap and GPU rendering.
4. Remember that Blender uses a three-button mouse workflow; enable *Emulate 3 Button Mouse* if you lack a middle button.

---

## The Interface

| Area | Purpose |
|---|---|
| **3D Viewport** (center) | Where you view and edit objects |
| **Outliner** (top right) | Lists every object and collection in the scene |
| **Properties editor** (bottom right) | Object, modifier, material, and render settings |
| **Timeline** (bottom) | Playback and keyframe controls |
| **Workspace tabs** (top) | Preset layouts: Modeling, Sculpting, Shading, Animation, etc. |
| **Toolbar / sidebar** | Tools and transform values (press `T` and `N`) |

---

## Key Concepts

- **Object Mode vs Edit Mode** – Object Mode moves whole objects; Edit Mode changes a mesh's vertices, edges, and faces (toggle with `Tab`).
- **Transforms** – move (`G`), rotate (`R`), and scale (`S`), optionally constrained to an axis by pressing `X`, `Y`, or `Z`.
- **Modifiers** – non-destructive operations such as Subdivision Surface, Mirror, Array, and Bevel.
- **Collections** – folders that group objects and control visibility.
- **Shader Editor** – build materials by connecting nodes.
- **Render engines** – *Eevee* for fast real-time results, *Cycles* for realistic path-traced rendering.
- **Geometry Nodes** – a node-based system for procedural modeling.

---

## Useful Shortcuts

| Shortcut | Action |
|---|---|
| `Tab` | Toggle Object / Edit Mode |
| `G` / `R` / `S` | Move / Rotate / Scale |
| `E` | Extrude (Edit Mode) |
| `Ctrl + R` | Loop cut |
| `Shift + A` | Add new object |
| `Ctrl + Z` | Undo |
| `Numpad 0` | Camera view |
| `F12` | Render image |

---

## Example Walkthrough

Modeling and rendering a simple mug:

1. Open Blender and delete the default cube (`X`).
2. Add a cylinder with `Shift + A` → **Mesh → Cylinder**.
3. Enter Edit Mode (`Tab`), select the top face, press `I` to inset, then `E` to extrude down to form the hollow.
4. Add a torus for the handle, then scale and position it against the cylinder body.
5. Add a **Subdivision Surface** modifier and a **Bevel** modifier for smooth edges.
6. In the Material tab, create a new material and set a base color.
7. Add a plane under the mug as the ground, and place a light and camera.
8. Switch the render engine to **Cycles**, press `F12`, and save the result with **Image → Save As**.

---

## Tips and Common Mistakes

- **Apply scale** (`Ctrl + A` → Scale) before modifying or exporting objects, or modifiers behave oddly.
- **Keep topology clean** with quads wherever possible.
- **Use modifiers rather than committing changes**, so you can adjust later.
- **Save incrementally** (`Ctrl + Alt + S` or new file names) since 3D scenes can become unstable.
- **Name your objects and materials** to keep larger scenes manageable.

---

## When To Choose Blender

Blender is a strong default for almost any 3D task, especially when budget matters. For strict engineering design with exact dimensions, a CAD tool like [AutoCAD](../Siulation/AutoCAD.md) is more appropriate. For animated graphics layered over video, you may combine Blender renders with [Adobe After Effects](../Motion%20Graphics/AdobeAfterEffects.md).

---

## Where To Go Next

- Follow the classic donut tutorial to learn the full pipeline.
- Learn UV unwrapping and texture painting to move beyond solid colors.
- Experiment with sculpting mode to create an organic character bust.

[⬅ Back to 3D Modeling Fundamentals](./[0]-Introduction-to-3DGraphics.md)