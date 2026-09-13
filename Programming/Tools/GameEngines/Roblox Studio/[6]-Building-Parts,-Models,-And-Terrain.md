[Previous](./[5]-Server-Scripts,-Local-Scripts,-And-RemoteEvents.md) | [Table of Contents](./[0]-Introduction-to-RobloxStudio.md) | [Next](./[7]-Roblox-Physics-And-Constraints.md)

*Advanced Experience Design*

# Lesson 6 - Building Parts, Models, And Terrain

## 6.1 Parts And Basic Building Tools

A **Part** is the fundamental physical building block in Roblox — a single solid shape (block, sphere, wedge, cylinder, or a custom mesh via `MeshPart`) with a position, size, rotation, color, and material.

You can insert Parts from the **Model** tab (Block, Sphere, Wedge, Cylinder tools) or by right-clicking `Workspace` in the Explorer and choosing **Insert Object > Part**. Once placed, the core building tools apply:

- **Move** — drag along X/Y/Z axis handles.
- **Scale** — resize along one axis or uniformly.
- **Rotate** — rotate around an axis, with optional snapping increments.

Key Part properties you'll adjust constantly in the Properties panel:

| Property | Effect |
|---|---|
| `Size` | Dimensions as a `Vector3` (X, Y, Z) |
| `Position` | World location as a `Vector3` |
| `Color` / `BrickColor` | Surface color |
| `Material` | Surface appearance (Plastic, Wood, Metal, Neon, Glass...) — affects both look and, for some materials, sound |
| `Anchored` | Whether physics affects it (covered fully in Lesson 7.1) |
| `CanCollide` | Whether other Parts/players physically collide with it |
| `Transparency` | 0 = fully visible, 1 = fully invisible |

---

## 6.2 Grouping Parts Into Models

Once you have more than one Part that logically belongs together (say, the several Parts making up a house or a vehicle), you group them into a **Model** — select the Parts in the viewport or Explorer, right-click, and choose **Group**.

```
Workspace
└── House (Model)
    ├── Walls (Part)
    ├── Roof (Part)
    ├── Door (Part)
    └── PrimaryPart → Walls
```

A Model's `PrimaryPart` (set in the Properties panel) acts as its reference point for movement and orientation — setting it lets code move or rotate the entire Model as a single unit via `Model:SetPrimaryPartCFrame()` (or the newer `Model:PivotTo()`), rather than repositioning every individual Part by hand.

Grouping matters beyond organization: a Model can be inserted into the Toolbox as a reusable asset, cloned as a single unit with `:Clone()`, and Destroyed as a single unit with `:Destroy()` — treating a whole structure as one logical object, much like a reusable scene in Godot's node system.

---

## 6.3 The Terrain Editor

For natural landscapes — hills, water, caves, cliffs — hand-placing Parts quickly becomes impractical. The **Terrain Editor** (Home tab > Terrain Editor) instead provides sculpting tools for a special voxel-based `Terrain` Instance found under `Workspace`:

```
┌─────────────────────────────┐
│  Terrain Editor               │
├─────────────────────────────┤
│  [Generate] [Add] [Subtract]  │
│  [Grow] [Erode] [Smooth]      │
│  [Paint] [Edit] [Region]      │
├─────────────────────────────┤
│  Material: Grass ▾            │
│  Brush Size: [====o----]      │
└─────────────────────────────┘
```

- **Generate** creates procedural terrain from presets (mountains, canyons, islands) as a fast starting point.
- **Add/Subtract** sculpt terrain by hand, like digital clay.
- **Paint** changes surface material (Grass, Sand, Rock, Water, Snow, etc.) without changing shape.
- Water added via Terrain automatically behaves as swimmable, physics-affecting water — no separate scripting required for basic buoyancy/swimming behavior.

Terrain is optimized differently from regular Parts (it's a single continuous voxel field rather than many individual objects), so it performs far better than trying to build natural landscapes out of thousands of small Parts.

---

## 6.4 Meshes And Imported Assets

Beyond primitive Parts and Terrain, Roblox supports **MeshParts** — custom 3D models imported from external tools like Blender, typically in `.fbx` format, then uploaded through Studio's **Asset Manager** (View tab > Asset Manager).

A MeshPart behaves like a regular Part for most purposes (it has `Position`, `Size`, physics properties) but its visual shape comes from imported geometry rather than a primitive, letting you bring in detailed custom-modeled characters, props, and environments beyond what block-building alone can achieve.

Common sources for mesh content:

- **Modeling it yourself** in Blender or a similar tool, then uploading through the Asset Manager.
- **The Toolbox** (Lesson 2.3), which includes thousands of free community and Roblox-made meshes.
- **The Marketplace**, Roblox's broader catalog of purchasable models, plugins, and assets, accessible from Studio or the Roblox website.

When importing meshes, keep in mind Roblox's platform-wide **polygon and file-size limits** per mesh (viewable during upload), since experiences also need to run acceptably on lower-end devices and mobile — a consideration worth remembering if you're bringing in highly detailed external models.

[Previous](./[5]-Server-Scripts,-Local-Scripts,-And-RemoteEvents.md) | [Table of Contents](./[0]-Introduction-to-RobloxStudio.md) | [Next](./[7]-Roblox-Physics-And-Constraints.md)
