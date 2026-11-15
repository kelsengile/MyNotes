[⬅ Back to README](../../../README.md)

<div align="center">
  <img src="https://cdn.jsdelivr.net/npm/simple-icons@latest/icons/autodesk.svg" width="50"/><br>
  <h1 style="margin-top: 0;">Introduction to Simulations</h1>
</div>

Simulation and technical design tools let you model real-world objects and systems precisely before building them. Engineers, architects, and designers use them to draft accurate plans, test dimensions and tolerances, and catch problems early, when they are still cheap to fix. This Topic covers the fundamentals of computer-aided design (CAD): how precise drawings are created, how 2D plans become 3D models, and how finished designs are documented and shared.

These lessons intentionally avoid tying the concepts to a single program. Once you understand the fundamentals here, head to the tool-specific file below to see how they are applied in practice.

### Why Learn Simulation And CAD?

- **Precision matters in the real world** — a drawing that is off by a few millimeters can mean a part that does not fit.
- **It lets you test before you build** — digital models are far cheaper to change than physical prototypes.
- **It's used across industries** — architecture, mechanical engineering, manufacturing, construction, and product design all rely on CAD.
- **It builds spatial reasoning** — you learn to think in dimensions, tolerances, and constraints.

A quick sense of how CAD differs from the illustration and 3D art tools you may already know:

| Aspect | CAD / Technical Design | Illustration | 3D Art |
|---|---|---|---|
| Main goal | Accurate, buildable designs | Visual appeal | Visual realism |
| Precision | Exact coordinates and dimensions | Approximate by eye | Approximate by eye |
| Typical output | Drawings, plans, manufacturing files | Images and graphics | Renders and animations |
| Measurement | Real-world units and tolerances | Pixels or points | Scene units |
| Common users | Engineers, architects, drafters | Designers, artists | Game and film artists |

### Tools:

The lessons below teach the fundamentals that apply across CAD and technical design software. To see those fundamentals applied to a specific tool, continue on to:

<table align="center" width="100%" style="margin-left:auto;margin-right:auto;table-layout:fixed;"> <tr><th align="center" width="25%">Tool</th><th align="center" width="75%">Description</th></tr> <tr> <td align="center" width="25%" height="100"><a href="./AutoCAD.md"><img src="https://cdn.jsdelivr.net/npm/simple-icons@latest/icons/autodesk.svg" width="40" title="AutoCAD"/><br>AutoCAD</a></td> <td align="left" width="75%">Professional CAD software for precise 2D drafting and 3D design, widely used in architecture and engineering.</td> </tr> </table>

---

### Table of Contents

**Basics**

   1. **[What Is Computer-Aided Design](#lesson-1---what-is-computer-aided-design)**  
       1.1 Defining CAD and Simulation  
       1.2 CAD vs Illustration  
       1.3 Units, Scale, and Precision  
       1.4 Industries and Use Cases  
   2. **[Coordinate Systems And Geometry](#lesson-2---coordinate-systems-and-geometry)**  
       2.1 Cartesian Coordinates  
       2.2 Absolute vs Relative Input  
       2.3 Points, Lines, and Curves  
       2.4 Snaps and Constraints  
   3. **[Drafting Fundamentals](#lesson-3---drafting-fundamentals)**  
       3.1 Drawing and Editing Objects  
       3.2 Layers and Organization  
       3.3 Dimensions and Annotations  
       3.4 Blocks and Reusable Symbols  

**Practice**

   4. **[From 2D Drawings To 3D Models](#lesson-4---from-2d-drawings-to-3d-models)**  
       4.1 Extruding and Revolving  
       4.2 Solids and Surfaces  
       4.3 Views and Projections  
       4.4 Assemblies and Parts  
   5. **[Testing And Reviewing Designs](#lesson-5---testing-and-reviewing-designs)**  
       5.1 Checking Fit and Tolerances  
       5.2 Measuring and Inspecting  
       5.3 Basic Simulation Concepts  
       5.4 Revising and Version Control  

**Ecosystem**

   6. **[Output And Collaboration](#lesson-6---output-and-collaboration)**  
       6.1 Printing and Plotting  
       6.2 Common File Formats (DWG, DXF, PDF)  
       6.3 Sharing With Teams and Clients  
       6.4 Standards and Conventions  
   7. **[Choosing A CAD Tool](#lesson-7---choosing-a-cad-tool)**  
       7.1 2D Drafting vs 3D Modeling  
       7.2 Free vs Paid Software  
       7.3 Industry-Specific Tools  
       7.4 Where to Go Next  

---

*Basics*

## Lesson 1 - What Is Computer-Aided Design

### 1.1 Defining CAD and Simulation

Computer-aided design (CAD) is the use of software to create precise drawings and models of real objects. Simulation extends this by testing how a design would behave, such as how it fits together, bears loads, or moves, before anything is built.

---

### 1.2 CAD vs Illustration

An illustration only needs to look right, while a CAD drawing must be exactly right. Every line in CAD has real coordinates and lengths, so the file can be used directly for planning and manufacturing.

---

### 1.3 Units, Scale, and Precision

Set your drawing units (millimeters, meters, inches, feet) before you start. Draw objects at real-world size (1:1) and apply scale only when printing. Mixing units is a classic source of costly errors.

---

### 1.4 Industries and Use Cases

CAD is used in architecture for floor plans, in mechanical engineering for parts, in electrical and plumbing for layouts, in construction for site plans, and in manufacturing for production drawings.

---

*Basics*

## Lesson 2 - Coordinate Systems And Geometry

### 2.1 Cartesian Coordinates

Every point is described by its position along the X and Y axes (and Z in 3D). The origin (0,0) is the reference point, and knowing where it sits helps you place objects accurately.

---

### 2.2 Absolute vs Relative Input

Absolute input gives a point's position from the origin, such as `10,5`. Relative input gives a point's position from the last point, such as `@10,5`. Polar input uses a distance and an angle, such as `@10<45`.

---

### 2.3 Points, Lines, and Curves

Drawings are built from points, straight lines, arcs, circles, and splines. Complex shapes are combinations of these simple pieces, and each one is defined by exact numbers.

---

### 2.4 Snaps and Constraints

Snaps lock the cursor to exact features like endpoints, midpoints, and centers. Constraints keep relationships fixed, such as keeping two lines parallel or a circle tangent to a line, even when other parts change.

---

*Basics*

## Lesson 3 - Drafting Fundamentals

### 3.1 Drawing and Editing Objects

Basic drawing commands create lines, rectangles, and circles. Editing commands such as move, copy, offset, trim, and extend reshape them. Most drafting is a rhythm of drawing a rough shape and then editing it precisely.

---

### 3.2 Layers and Organization

Layers group objects by purpose, such as walls, dimensions, and notes. Each layer has its own color and linetype and can be hidden or locked, which keeps complex drawings readable.

---

### 3.3 Dimensions and Annotations

Dimensions state measurements, and annotations add text, notes, and symbols. They are what let a builder or machinist understand the drawing without guessing.

---

### 3.4 Blocks and Reusable Symbols

A block is a saved group of objects, such as a door, bolt, or window, that can be inserted many times. Editing the block definition updates every copy.

---

*Practice*

## Lesson 4 - From 2D Drawings To 3D Models

### 4.1 Extruding and Revolving

Extruding pushes a 2D shape along a straight path to give it thickness. Revolving spins a shape around an axis to create round forms such as cylinders and bottles.

---

### 4.2 Solids and Surfaces

Solids have volume and behave like real objects, so you can measure mass and cut holes. Surfaces are thin skins that are useful for complex curved shapes.

---

### 4.3 Views and Projections

Standard views (top, front, side) and an isometric view show a 3D model from different angles. Drawing views generated from the model stay in sync when the design changes.

---

### 4.4 Assemblies and Parts

A part is a single component, and an assembly combines parts into a product. Assembling them reveals fit problems, such as a bolt that doesn't line up with its hole.

---

*Practice*

## Lesson 5 - Testing And Reviewing Designs

### 5.1 Checking Fit and Tolerances

Tolerance is the allowed variation in a measurement. Checking that parts fit within their tolerances prevents problems such as a shaft that is too large for its bearing.

---

### 5.2 Measuring and Inspecting

Use measuring tools to check distances, angles, areas, and volumes in the model. Regular checks while drafting catch mistakes long before they reach production.

---

### 5.3 Basic Simulation Concepts

Simulations apply conditions such as forces, heat, or motion to a model to predict how it behaves. Inputs include materials, loads, and constraints, and the results guide design improvements.

---

### 5.4 Revising and Version Control

Save numbered versions, record what changed, and keep older files so you can go back. Clear revision notes help teams know which drawing is current.

---

*Ecosystem*

## Lesson 6 - Output And Collaboration

### 6.1 Printing and Plotting

Plotting turns a drawing into a scaled printout or PDF. Set the paper size, scale, and line weights so the printed result matches the intended standard.

---

### 6.2 Common File Formats (DWG, DXF, PDF)

DWG is a common native CAD format. DXF is a more open exchange format that many programs can read. PDF is used for sharing finished drawings that don't need editing.

---

### 6.3 Sharing With Teams and Clients

Share files in formats others can open, and include any referenced files or fonts. Use clear file names and a consistent folder structure so everyone finds the right version.

---

### 6.4 Standards and Conventions

Industries follow drawing standards for line types, symbols, text sizes, and title blocks. Following them makes your drawings understandable to anyone in the field.

---

*Ecosystem*

## Lesson 7 - Choosing A CAD Tool

### 7.1 2D Drafting vs 3D Modeling

2D drafting is fast and well suited to plans and simple parts. 3D modeling takes more effort but allows visualization, fit checking, and simulation. Many workflows use both.

---

### 7.2 Free vs Paid Software

Professional CAD is usually paid, though trials, student licenses, and free or open-source options exist. Pick the tool that matches the file formats and standards your industry uses.

---

### 7.3 Industry-Specific Tools

Architecture, mechanical design, electronics, and civil engineering each have specialized CAD tools with built-in libraries and checks. General-purpose CAD is a good foundation before specializing.

---

### 7.4 Where to Go Next

Open the AutoCAD file in this topic and draw a simple room plan with layers and dimensions. Then try extruding a 2D profile into a 3D solid.