[⬅ Back to README](../../../README.md)

<div align="center">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/blender/blender-original.svg" width="50"/><br>
  <h1 style="margin-top: 0;">Introduction to 3D Modeling</h1>
</div>

3D modeling is the process of building objects, characters, and environments in three-dimensional space, then texturing, lighting, and rendering them into still images or animations. It powers video games, film and TV visual effects, product visualization, architecture, and 3D printing. This Topic covers the fundamentals behind 3D work: how models are built from meshes, how materials and lighting create realism, and how a finished scene is rendered and exported.

These lessons intentionally avoid tying the concepts to a single program. Once you understand the fundamentals here, head to the tool-specific files below to see how they are put into practice with real software.

### Why Learn 3D Modeling?

- **It's used across many industries** — games, film, architecture, product design, medicine, and manufacturing all rely on 3D assets.
- **It combines art and technical thinking** — you learn form and composition alongside geometry and optimization.
- **It's easy to start for free** — Blender offers a complete professional pipeline at no cost.
- **Skills transfer between tools** — meshes, UVs, materials, and rendering are the same ideas in nearly every 3D package.

A quick sense of the most common ways to build a 3D model:

| Approach | How It Works | Best For |
|---|---|---|
| Polygon modeling | Build shapes from vertices, edges, and faces | Hard-surface objects, game assets |
| Sculpting | Push and pull a dense mesh like digital clay | Characters, creatures, organic forms |
| Procedural / nodes | Generate geometry from rules and parameters | Terrain, patterns, variations |
| CAD modeling | Define exact dimensions and constraints | Engineering and manufacturing parts |

### Tools:

The lessons below teach the fundamentals that apply across 3D software. To see those fundamentals applied to a specific tool, continue on to:

<table align="center" width="100%" style="margin-left:auto;margin-right:auto;table-layout:fixed;"> <tr><th align="center" width="25%">Tool</th><th align="center" width="75%">Description</th></tr> <tr> <td align="center" width="25%" height="100"><a href="./Blender.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/blender/blender-original.svg" width="40" title="Blender"/><br>Blender</a></td> <td align="left" width="75%">Free, open-source suite for modeling, sculpting, animation, simulation, and rendering.</td> </tr> </table>

---

### Table of Contents

**Basics**

   1. **[What Is 3D Modeling](#lesson-1---what-is-3d-modeling)**  
       1.1 Defining 3D Modeling  
       1.2 Vertices, Edges, and Faces  
       1.3 Coordinate Space and Units  
       1.4 Common Use Cases  
   2. **[Meshes And Topology](#lesson-2---meshes-and-topology)**  
       2.1 Polygons, Triangles, and Quads  
       2.2 Clean Topology and Edge Flow  
       2.3 Polygon Count and Performance  
       2.4 Normals and Shading  
   3. **[Building Models](#lesson-3---building-models)**  
       3.1 Polygon Modeling  
       3.2 Sculpting  
       3.3 Modifiers and Non-Destructive Workflows  
       3.4 Reference Images and Blocking Out  

**Look And Output**

   4. **[Materials And Textures](#lesson-4---materials-and-textures)**  
       4.1 UV Mapping  
       4.2 Image Textures and Procedural Textures  
       4.3 Materials and Shaders  
       4.4 PBR Workflow  
   5. **[Lighting Cameras And Rendering](#lesson-5---lighting-cameras-and-rendering)**  
       5.1 Types of Lights  
       5.2 Camera Settings and Composition  
       5.3 Real-Time vs Path-Traced Rendering  
       5.4 Render Settings and Output  

**Ecosystem**

   6. **[Animation And Rigging Basics](#lesson-6---animation-and-rigging-basics)**  
       6.1 Keyframes and Timelines  
       6.2 Armatures and Rigging  
       6.3 Weight Painting  
       6.4 Simple Animation Workflow  
   7. **[Choosing A 3D Tool](#lesson-7---choosing-a-3d-tool)**  
       7.1 Modeling vs Sculpting vs CAD  
       7.2 Free vs Paid Software  
       7.3 Exporting to Games and 3D Printing  
       7.4 Where to Go Next  

---

*Basics*

## Lesson 1 - What Is 3D Modeling

### 1.1 Defining 3D Modeling

3D modeling means creating a digital object with width, height, and depth that can be viewed from any angle. The model describes shape; looks and motion are added afterward.

---

### 1.2 Vertices, Edges, and Faces

A vertex is a point in space, an edge connects two vertices, and a face is a flat surface enclosed by edges. Together they form a mesh, the basic building block of most 3D models.

---

### 1.3 Coordinate Space and Units

3D space uses X, Y, and Z axes. Objects have a location, rotation, and scale, and most software lets you set real-world units so a model's size makes sense when exported.

---

### 1.4 Common Use Cases

Games need efficient, low-polygon assets. Film and visualization favor detail and realism. Architecture and product design focus on accuracy, and 3D printing needs watertight, printable shapes.

---

*Basics*

## Lesson 2 - Meshes And Topology

### 2.1 Polygons, Triangles, and Quads

Faces are made of polygons: triangles (three sides), quads (four sides), or n-gons (more). Quads are preferred for modeling because they subdivide and deform predictably, while game engines convert everything to triangles.

---

### 2.2 Clean Topology and Edge Flow

Topology is how a mesh's edges are arranged. Good edge flow follows the natural form of the object, such as loops around an eye or a joint, so surfaces smooth and bend without ugly pinching.

---

### 2.3 Polygon Count and Performance

More polygons mean more detail but also slower viewports, longer renders, and heavier game assets. Model only as much detail as the final use needs, and use textures or normal maps to fake fine detail.

---

### 2.4 Normals and Shading

Normals are directions that tell the renderer which way a face points. Flipped normals cause dark or invisible faces. Smooth shading blends lighting across faces, while flat shading shows each face separately.

---

*Basics*

## Lesson 3 - Building Models

### 3.1 Polygon Modeling

Start with a simple shape and refine it with extrude, inset, bevel, and loop cuts. This is the standard approach for hard-surface objects like furniture, vehicles, and props.

---

### 3.2 Sculpting

Sculpting pushes, pulls, and smooths a dense mesh like clay. It suits organic forms such as characters and creatures, and the result is often retopologized into a cleaner, lighter mesh.

---

### 3.3 Modifiers and Non-Destructive Workflows

Modifiers (mirror, subdivision, array, bevel) apply changes without permanently altering the mesh. Keeping them adjustable lets you revise a design late in the process.

---

### 3.4 Reference Images and Blocking Out

Gather reference images first, then block out the overall proportions with simple shapes before adding detail. Getting the big forms right early saves a lot of rework.

---

*Look And Output*

## Lesson 4 - Materials And Textures

### 4.1 UV Mapping

UV mapping unfolds a 3D surface into a flat 2D layout so images can be wrapped onto it. Clean seams and even texel density keep textures from stretching.

---

### 4.2 Image Textures and Procedural Textures

Image textures are painted or photographed pictures applied to a model. Procedural textures are generated by math, so they have no fixed resolution and can be tweaked with sliders.

---

### 4.3 Materials and Shaders

A material defines how a surface reacts to light: color, roughness, metalness, transparency, and glow. A shader is the program that calculates that response.

---

### 4.4 PBR Workflow

Physically based rendering uses maps such as base color, roughness, metallic, and normal to describe a surface. Because PBR follows real-world light behavior, materials look consistent across different engines and lighting.

---

*Look And Output*

## Lesson 5 - Lighting Cameras And Rendering

### 5.1 Types of Lights

Common lights are point, spot, sun (directional), and area lights. Many scenes also use an HDRI environment image for realistic ambient light and reflections.

---

### 5.2 Camera Settings and Composition

Focal length changes perspective, depth of field blurs the background, and framing guides the viewer's eye. The same composition rules from photography and 2D design apply.

---

### 5.3 Real-Time vs Path-Traced Rendering

Real-time renderers draw images instantly and suit games and previews. Path tracers simulate how light bounces and give more realistic results, but take longer per frame.

---

### 5.4 Render Settings and Output

Choose a resolution, sample count, and file format. Use more samples or denoising to reduce grain, and render to PNG or EXR if you plan to edit the image afterward.

---

*Ecosystem*

## Lesson 6 - Animation And Rigging Basics

### 6.1 Keyframes and Timelines

Keyframes record a property, such as position or rotation, at specific frames, and the software fills in the motion between them. The timeline is where you place and adjust them.

---

### 6.2 Armatures and Rigging

An armature is a skeleton of bones placed inside a model. Rigging connects the model to the bones and adds controls so a character can be posed easily.

---

### 6.3 Weight Painting

Weight painting defines how strongly each bone influences nearby vertices. Good weights make joints bend smoothly instead of collapsing or stretching.

---

### 6.4 Simple Animation Workflow

Plan the motion, block out key poses, add in-between frames, then refine timing and easing. A bouncing ball is the classic first exercise.

---

*Ecosystem*

## Lesson 7 - Choosing A 3D Tool

### 7.1 Modeling vs Sculpting vs CAD

Use polygon or sculpting tools for artistic work and CAD tools when exact dimensions matter. Some programs, like Blender, cover the artistic side in one package.

---

### 7.2 Free vs Paid Software

Blender is free and complete enough for professional work. Paid tools often offer specialized workflows or industry integration, but are not required to learn the fundamentals.

---

### 7.3 Exporting to Games and 3D Printing

Games typically use FBX or glTF with optimized meshes and baked textures. 3D printing uses STL or 3MF and needs closed, manifold geometry with correct scale.

---

### 7.4 Where to Go Next

Start with the Blender file in this topic and model a simple object such as a mug. From there, add materials, lighting, and a render, then try sculpting or animation.