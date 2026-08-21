[Previous](./[17]-Cameras-in-2D.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[19]-Lighting-and-Shading-Basics.md)

*3D Game Development*

# Lesson 18 - 3D Models, Meshes & Materials

## 18.1 Meshes and Vertices

A **mesh** is the 3D geometry that defines an object's shape, built from:

- **Vertices** — individual points in 3D space.
- **Edges** — lines connecting two vertices.
- **Faces (usually triangles)** — flat surfaces formed by connecting three or more vertices, which is what actually gets rendered.

A model's **poly count** (number of triangles/faces) affects performance — more detailed meshes cost more to render, which matters when many objects are on screen at once (see Lesson 40 on performance).

---

## 18.2 Materials and Shaders (Overview)

A **material** describes how a mesh's surface should look — its color, shininess, roughness, and how it reacts to light. Under the hood, a material is powered by a **shader**, a small program that runs on the GPU and calculates the final color of each pixel drawn.

Most engines ship with ready-made shaders (like Unity's Standard Shader or Godot's StandardMaterial3D) covering common needs, so beginners rarely need to write custom shader code to get realistic-looking materials.

---

## 18.3 Textures and UV Mapping

- A **texture** is a 2D image applied to a 3D mesh's surface — like wrapping paper around a shape.
- **UV mapping** defines how that 2D image wraps onto the 3D mesh, by assigning each vertex a corresponding 2D coordinate (U, V) on the texture.

Common texture types layered together on one material include:

- **Albedo/diffuse** — the base color.
- **Normal map** — fakes extra surface detail (bumps, grooves) without adding more geometry.
- **Roughness/metallic maps** — control how shiny or matte different parts of the surface appear.

---

## 18.4 Importing 3D Models

3D models are usually created in external tools (Blender, Maya, 3ds Max) and imported into the engine via common file formats like `.fbx`, `.gltf/.glb`, or `.obj`. When importing:

- Check the **scale** — a model exported at the wrong scale can appear absurdly large or tiny in-engine.
- Confirm **normals** (surface direction) are correct, or lighting will look inverted or broken.
- Import associated **animations** and **materials** if the format supports them, or set those up separately in-engine.

[Previous](./[17]-Cameras-in-2D.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./[19]-Lighting-and-Shading-Basics.md)
