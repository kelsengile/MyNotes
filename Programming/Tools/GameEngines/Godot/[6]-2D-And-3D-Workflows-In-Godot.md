[Previous](./[5]-Signals-And-Communication-Between-Nodes.md) | [Table of Contents](./[0]-Introduction-to-Godot.md) | [Next](./[7]-Physics-And-Collision-In-Godot.md)

*Building A Game*

# Lesson 6 - 2D And 3D Workflows In Godot

## 6.1 Sprite2D, AnimatedSprite2D, And TileMaps

Godot's 2D nodes are all built around `CanvasItem`, which gives them a position, rotation, scale, and visibility on screen.

- **`Sprite2D`** — displays a single static texture. The simplest way to show an image.

```gdscript
# Swapping a sprite's texture at runtime
$Sprite2D.texture = preload("res://art/player_hurt.png")
```

- **`AnimatedSprite2D`** — displays a `SpriteFrames` resource: a set of named animations, each made of a sequence of frames. Ideal for classic 2D sprite-sheet animation (walk cycles, idle loops, attacks).

```gdscript
$AnimatedSprite2D.play("walk")
$AnimatedSprite2D.play("idle")
```

- **`TileMap`** — the standard way to build 2D levels out of a reusable grid of tiles (a **TileSet**), rather than placing hundreds of individual sprites by hand. You paint tiles directly in the editor using a dedicated TileMap painting mode, and can define per-tile collision, navigation, and custom data all from the same TileSet resource.

```
TileMap (grass, dirt, water tiles painted in editor)
├── Layer 0: Ground
├── Layer 1: Decoration (fences, rocks)
└── Layer 2: Collision (auto-generated from tile data)
```

Together, these three nodes cover the vast majority of what a 2D game needs for visuals and level layout.

---

## 6.2 3D Meshes And The Node3D Hierarchy

3D scenes are built around `Node3D` instead of `Node2D` — it provides a 3D transform (position, rotation, scale as `Vector3`s) instead of a 2D one.

- **`MeshInstance3D`** — the 3D equivalent of `Sprite2D`: it displays a **mesh** (geometry) using a **material** (surface appearance). Meshes are usually imported from `.gltf`/`.glb` files exported by modeling tools like Blender, though Godot also includes simple primitive meshes (`BoxMesh`, `SphereMesh`, `CylinderMesh`) useful for prototyping levels before final art exists.
- **`Light3D` family** (`DirectionalLight3D`, `OmniLight3D`, `SpotLight3D`) — light sources that affect how meshes are shaded.
- **`WorldEnvironment`** — controls global rendering settings for the scene: sky, fog, ambient light, and post-processing effects like glow or tonemapping.

A minimal 3D scene tree:

```
Level (Node3D)
├── DirectionalLight3D
├── WorldEnvironment
├── MeshInstance3D (Ground)
└── Player (CharacterBody3D)
    ├── MeshInstance3D
    ├── CollisionShape3D
    └── Camera3D
```

Note the parallel structure with 2D — `CharacterBody3D` and `CollisionShape3D` mirror their 2D counterparts closely, which is intentional: Godot's 2D and 3D APIs are designed to feel as consistent as possible so skills transfer between them.

---

## 6.3 Cameras In Godot (Camera2D/Camera3D)

Without a camera node, Godot has nothing to render from — you need at least one **active** camera in a running scene.

**`Camera2D`**
- Typically a child of the player, so the view follows them automatically.
- Key properties: `zoom` (how much of the world is visible), `position_smoothing_enabled` (adds a lag/lerp effect so the camera doesn't snap rigidly to the player), and **limits** (`limit_left/right/top/bottom`), used to stop the camera from showing empty space past a level's edges.

**`Camera3D`**
- Key properties: `fov` (field of view, in degrees), `near`/`far` clipping planes (the range of distances actually rendered), and `projection` (Perspective vs Orthographic).
- Only one camera can be `current` at a time per viewport; if you have multiple `Camera3D` nodes (e.g. a security-camera minigame), you switch between them by calling `.make_current()` on the one you want active.

```gdscript
# Switching to a different 3D camera
$SecurityCamera.make_current()
```

---

## 6.4 Choosing Between 2D And 3D Nodes For A Project

It's worth deciding early, since a project's root scene structure and the physics/rendering nodes you use throughout depend on it. Some guidance:

| If your game... | Consider |
|---|---|
| Is a platformer, top-down game, puzzle game, or visual-novel style game | **2D** |
| Needs real depth, first/third-person movement, or 3D-modeled environments | **3D** |
| Is 2D visually but you want "2.5D" depth effects (parallax, faux-3D lighting) | **2D**, using `CanvasLayer`/`ParallaxBackground`/2D lighting tools |
| Uses 3D models but is viewed from a fixed isometric/top-down angle | Often **3D** with an orthographic `Camera3D` — gives real depth and shadows while keeping controls simple |

It's entirely possible — and common — to mix both: a 3D game with a 2D UI layer (via `CanvasLayer`, since UI should stay screen-space rather than move with the 3D camera), or a 2D game with a 3D-rendered background for parallax depth. What matters is picking the *primary* gameplay space early, since your physics bodies, collision shapes, and movement scripts in the next lesson will follow directly from that choice.

[Previous](./[5]-Signals-And-Communication-Between-Nodes.md) | [Table of Contents](./[0]-Introduction-to-Godot.md) | [Next](./[7]-Physics-And-Collision-In-Godot.md)
