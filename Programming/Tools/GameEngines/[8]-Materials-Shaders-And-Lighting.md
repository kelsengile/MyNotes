[Previous](./[7]-Introduction-To-Rendering.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[9]-2D-Vs-3D-Rendering.md)

*Rendering*

# Lesson 8 - Materials, Shaders, And Lighting

## 8.1 Materials And Textures

A **material** describes how the surface of an object should look — its color, shininess, roughness, and more. Materials are typically built from one or more **textures**: 2D images that get "wrapped" onto a 3D model's surface, similar to wrapping paper around a box.

Common texture types used within a material include:

- **Albedo/diffuse texture** — the base color of the surface.
- **Normal map** — a texture that fakes small surface bumps and dents without adding actual geometry, by altering how light bounces off the surface.
- **Roughness/metallic maps** — control how shiny or matte, and how metal-like or non-metal-like, a surface appears.

A single 3D model can reuse the same material across many instances — a hundred identical crates in a warehouse level can all share one `Crate` material, keeping memory usage low and ensuring a consistent look.

**Example `Metal_Rusted` material:**

| Texture slot | What it contributes |
|---|---|
| Albedo | Reddish-brown rust coloring with dirt streaks |
| Normal map | Small pits, scratches, and dents in the surface |
| Roughness map | Rough in rusted patches, slightly shinier on exposed metal |
| Metallic map | High metallic value under the rust, near-zero on flaking paint |

---

## 8.2 What Is a Shader

A **shader** is a small program that runs directly on the GPU and determines exactly how a material is rendered — how light interacts with it, what color each pixel ends up, and any special visual effects applied to it. Shaders are what actually calculate the final result described conceptually by a material.

There are several common types of shaders, corresponding to stages of the rendering pipeline from Lesson 7:

- **Vertex shaders** — run once per vertex, typically handling the transform math that positions a model correctly on screen.
- **Fragment (pixel) shaders** — run once per pixel, calculating the final color based on textures, lighting, and other inputs.

Most engines let developers write custom shaders for special effects (water, force fields, toon-style outlines), while also providing ready-made, general-purpose shaders so that developers who aren't graphics specialists can still get good-looking results without writing shader code themselves.

A minimal fragment shader (simplified, pseudocode style) that just outputs a texture's color might look like:

```
fragment_shader(uv_coordinates) {
    color = sample_texture(albedo_texture, uv_coordinates)
    return color
}
```

A more advanced version would also factor in lighting direction, surface normals, and shadows before producing the final color.

---

## 8.3 Lighting Models

A **lighting model** is the mathematical approach a renderer uses to simulate how light interacts with surfaces. Two broad philosophies dominate modern engines:

- **Physically Based Rendering (PBR)** — simulates light using formulas based on real-world physics, using material properties like roughness and metallicness to produce realistic results that look consistent under any lighting condition. Most modern engines (Unity, Unreal, Godot) default to PBR.
- **Stylized/non-photorealistic lighting** — deliberately simplifies or distorts realistic lighting to achieve an artistic look, such as flat "toon shading" with hard-edged color bands instead of smooth gradients.

Engines also distinguish between different **light types** placed in a scene, including:

- **Directional lights** — simulate an infinitely distant light source like the sun; all light rays are parallel.
- **Point lights** — emit light in all directions from a single point, like a light bulb.
- **Spot lights** — emit a cone of light from a point, like a flashlight.

| Light type | Real-world analogy | Common use |
|---|---|---|
| Directional | The sun | Outdoor daylight scenes |
| Point | A light bulb | Torches, lanterns, explosions |
| Spot | A flashlight | Headlights, stage lights, stealth game sightlines |

---

## 8.4 Shadows

Shadows are essential for making a scene feel grounded and three-dimensional — without them, objects can appear to float above the surfaces beneath them. Most engines compute shadows using a technique called **shadow mapping**: the scene is rendered once from the perspective of each light source, recording how far away the nearest surface is at every point. When rendering the final image from the camera's perspective, the engine checks whether a given point is farther from the light than what was recorded — if so, that point lies in shadow.

Shadows are expensive to compute well, so engines offer many quality and performance trade-offs, such as:

- **Shadow resolution** — higher resolution shadow maps look crisper but cost more performance and memory.
- **Shadow distance** — limiting how far from the camera shadows are calculated at all, since distant shadows are rarely noticeable.
- **Soft vs hard shadows** — soft shadows have blurred, realistic edges (more expensive) while hard shadows have sharp, defined edges (cheaper).

Because lighting and shadows are often the most expensive part of rendering a frame, they're one of the first places developers look when optimizing a game's performance — a topic covered further in Lesson 17.

---

[Previous](./[7]-Introduction-To-Rendering.md) | [Table of Contents](./[0]-Introduction-to-GameEngines.md) | [Next](./[9]-2D-Vs-3D-Rendering.md)
