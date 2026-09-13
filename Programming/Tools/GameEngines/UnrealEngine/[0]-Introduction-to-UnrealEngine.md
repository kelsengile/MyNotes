[⬅ Back to Game Engine Fundamentals](../[0]-Introduction-to-GameEngines.md)

# Introduction to Unreal Engine

Unreal Engine, developed by Epic Games, is known for its high-fidelity, high-performance rendering and is widely used in AAA game production as well as film and architectural visualization. It's built around an **Actor + Component** architecture, and uniquely offers two first-class ways to write behavior side by side: **C++** for performance-critical systems, and **Blueprints**, a visual scripting system that lets logic be built by connecting nodes instead of writing text-based code.

This Topic takes the tool-agnostic ideas from Game Engine Fundamentals — scenes, transforms, rendering, physics, scripting — and grounds them in Unreal's specific editor, terminology, and workflow. By the end, you should be comfortable navigating the Unreal Editor, building Actors out of components, scripting behavior in Blueprints (and, at a basic level, C++), and packaging a project for a target platform.

## Why Learn Unreal Engine?

- **Industry-leading visual fidelity** — Unreal's rendering pipeline, including modern systems like Nanite and Lumen, is a benchmark for realistic, high-end 3D graphics.
- **Blueprints lower the barrier to entry** — designers and artists can build real gameplay logic visually, without needing to write C++ first.
- **Used across AAA and beyond** — Unreal powers major titles as well as a large share of film previsualization and virtual production work, making the skill transferable outside games.
- **Free to start, revenue-based licensing** — Unreal is free to download and use, with royalties only owed once a project earns above a defined revenue threshold.
- **A complete production toolset** — sequencer for cinematics, a full animation system, and built-in networking are included rather than added on.

Download: [unrealengine.com/download](https://www.unrealengine.com/download)

## Table of Contents

**Getting Started**

   1. **[Installing Unreal Engine And Epic Games Launcher](./[1]-Installing-Unreal-Engine-And-Epic-Games-Launcher.md)**  
       1.1 Epic Games Launcher And Account Setup  
       1.2 Engine Versions And Installing Unreal  
       1.3 Creating A New Project And Templates  
       1.4 Unreal's Licensing Model  
   2. **[The Unreal Editor Interface](./[2]-The-Unreal-Editor-Interface.md)**  
       2.1 The Viewport And Navigation  
       2.2 The World Outliner  
       2.3 The Details Panel And Content Browser  
       2.4 Modes And Toolbars  

**Core Concepts And Scripting**

   3. **[Actors, Components, And Blueprints In Unreal](./[3]-Actors,-Components,-And-Blueprints-In-Unreal.md)**  
       3.1 Actors As Unreal's Building Block  
       3.2 Components (Static Mesh, Collision, Camera)  
       3.3 Blueprint Classes vs C++ Classes  
       3.4 The Actor Lifecycle (BeginPlay, Tick)  
   4. **[Introduction To Blueprint Visual Scripting](./[4]-Introduction-To-Blueprint-Visual-Scripting.md)**  
       4.1 The Blueprint Editor Layout  
       4.2 Nodes, Pins, And Execution Flow  
       4.3 Variables And Functions In Blueprints  
       4.4 Event Graphs vs Function Graphs  
   5. **[Introduction To C++ In Unreal Engine](./[5]-Introduction-To-C%2B%2B-In-Unreal-Engine.md)**  
       5.1 Why Use C++ Alongside Blueprints  
       5.2 UCLASS, UPROPERTY, And UFUNCTION Basics  
       5.3 Creating A C++ Actor Class  
       5.4 Blueprint/C++ Interoperability  

**Building Features**

   6. **[Unreal Physics And Collision](./[6]-Unreal-Physics-And-Collision.md)**  
       6.1 Collision Presets And Object/Trace Channels  
       6.2 Simulating Physics On A Component  
       6.3 Overlap Events vs Hit Events  
       6.4 Physics Constraints  
   7. **[UMG - The Unreal Motion Graphics UI Designer](./[7]-UMG---The-Unreal-Motion-Graphics-UI-Designer.md)**  
       7.1 Widget Blueprints  
       7.2 The Designer And Graph Tabs  
       7.3 Common Widgets (Text, Button, Canvas Panel)  
       7.4 Adding Widgets To The Viewport  
   8. **[Unreal's Animation System (Animation Blueprints)](./[8]-Unreal's-Animation-System-(Animation-Blueprints).md)**  
       8.1 Skeletal Meshes And Skeletons  
       8.2 Animation Sequences And Montages  
       8.3 Animation Blueprints And State Machines  
       8.4 Blend Spaces  

**Shipping**

   9. **[Packaging And Deploying An Unreal Project](./[9]-Packaging-And-Deploying-An-Unreal-Project.md)**  
       9.1 Project Settings Before Packaging  
       9.2 Packaging For A Platform  
       9.3 Cooking Content  
       9.4 Publishing To Stores  
