[Previous](./[14]-Sprites-and-Spritesheets.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[16]-2D-Physics-and-Collisions.md)

*2D Game Development*

# Lesson 15 - Tilemaps & Level Design

## 15.1 What Is a Tilemap?

A **tilemap** is a grid-based system for building 2D levels out of small, reusable square (or hexagonal) tiles, rather than placing individual sprites by hand. Instead of dragging hundreds of separate wall sprites into a scene, you "paint" a level using a small set of tile pieces, much like a digital version of a tile-based board game.

---

## 15.2 Tilesets and Tile Palettes

- A **tileset** is the source spritesheet containing all the individual tile images (grass, dirt, wall corners, water, etc.).
- A **tile palette** is the in-editor tool used to select a tile from the tileset and paint it onto the tilemap grid, similar to a paint bucket tool in image-editing software.

Well-designed tilesets include variant tiles for edges and corners (e.g. a "grass-to-dirt transition" tile) so levels don't look like a harsh grid of repeated squares.

---

## 15.3 Layers

Tilemaps commonly use multiple stacked layers:

- **Ground layer** — the walkable floor.
- **Collision layer** — invisible or visible tiles that block movement (walls, obstacles).
- **Decoration layer** — non-solid visual details drawn on top (grass tufts, cracks, debris).
- **Background layer** — drawn behind everything else, often with parallax scrolling (Lesson 17).

Separating concerns into layers keeps a level both easy to edit and easy to reason about — collision logic only needs to check the collision layer, not every visual tile.

---

## 15.4 Designing Levels with Tiles

A few practical tips for building levels with tilemaps:

- **Block out with simple shapes first** — use one or two placeholder tiles to test whether the level's layout and pacing feel right before adding detailed art.
- **Use consistent grid sizes** — mixing tile sizes within a tilemap causes visual misalignment.
- **Think in terms of player flow** — where does the player enter, what draws their attention, and where does the path lead next?
- **Combine tilemaps with hand-placed objects** — background/terrain works well as tiles, while important elements (NPCs, unique props, triggers) are usually placed individually so they can carry their own logic.

[Previous](./[14]-Sprites-and-Spritesheets.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[16]-2D-Physics-and-Collisions.md)
