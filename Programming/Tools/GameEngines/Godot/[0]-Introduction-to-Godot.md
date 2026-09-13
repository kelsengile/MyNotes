[⬅ Back to Game Engine Fundamentals](../[0]-Introduction-to-GameEngines.md)

# Introduction to Godot

Godot is a free and open-source game engine built around a lightweight editor and a flexible **node** system, where every piece of a game — a sprite, a sound, a piece of UI, a physics body — is a node that can be composed into a tree of other nodes. It supports both 2D and 3D development, ships its own beginner-friendly scripting language (GDScript), and has no royalties, revenue splits, or paid tiers of any kind.

This Topic takes the tool-agnostic concepts from Game Engine Fundamentals — scenes, transforms, rendering, physics, scripting — and grounds them in Godot's specific editor, workflow, and terminology. By the end, you should be comfortable opening the Godot editor, building a scene out of nodes, scripting behavior in GDScript, and exporting a finished project to a real platform.

## Why Learn Godot?

- **It's completely free** — Godot is MIT-licensed with no royalties or revenue thresholds, unlike some competing engines.
- **Lightweight and fast to learn** — the editor is small, opens quickly, and its node-based approach maps intuitively onto how a game is actually structured.
- **Excellent for 2D** — Godot's 2D tools (TileMaps, Sprite2D, its 2D physics engine) are considered some of the strongest available in any engine.
- **Beginner-friendly scripting** — GDScript is intentionally close to Python in syntax, making it approachable for first-time programmers, while C# remains available for those who want it.
- **Open source** — the full engine source is public, so nothing is hidden behind a closed editor, and the community can (and does) contribute fixes and features directly.

Download: [godotengine.org/download](https://godotengine.org/download/)

## Table of Contents

**Getting Started**
   1. **[Installing And Setting Up Godot](./[1]-Installing-And-Setting-Up-Godot.md)**  
       1.1 Downloading Godot (Standard vs .NET Builds)  
       1.2 First Launch And The Project Manager  
       1.3 Creating A New Project  
       1.4 Godot's Version History And Release Channels  
   2. **[The Godot Editor Interface](./[2]-The-Godot-Editor-Interface.md)**  
       2.1 The Main Editor Layout (Viewport, Scene Dock, FileSystem, Inspector)  
       2.2 The 2D And 3D Viewports  
       2.3 The Bottom Panels (Output, Debugger, Animation)  
       2.4 Customizing The Workspace  

**Core Concepts**
   3. **[Nodes And The Scene Tree](./[3]-Nodes-And-The-Scene-Tree.md)**  
       3.1 What Is A Node  
       3.2 Building A Scene Tree  
       3.3 Scenes As Reusable Building Blocks  
       3.4 Instancing Scenes Within Scenes  
   4. **[GDScript Fundamentals](./[4]-GDScript-Fundamentals.md)**  
       4.1 GDScript Syntax Basics  
       4.2 Variables, Types, And Export  
       4.3 Functions And The _ready/_process Callbacks  
       4.4 GDScript vs C# In Godot  
   5. **[Signals And Communication Between Nodes](./[5]-Signals-And-Communication-Between-Nodes.md)**  
       5.1 What Is A Signal  
       5.2 Connecting Signals In The Editor And In Code  
       5.3 Custom Signals  
       5.4 Groups And Node Communication Patterns  

**Building A Game**
   6. **[2D And 3D Workflows In Godot](./[6]-2D-And-3D-Workflows-In-Godot.md)**  
       6.1 Sprite2D, AnimatedSprite2D, And TileMaps  
       6.2 3D Meshes And The Node3D Hierarchy  
       6.3 Cameras In Godot (Camera2D/Camera3D)  
       6.4 Choosing Between 2D And 3D Nodes For A Project  
   7. **[Physics And Collision In Godot](./[7]-Physics-And-Collision-In-Godot.md)**  
       7.1 CharacterBody, RigidBody, And StaticBody  
       7.2 Collision Shapes And Layers/Masks  
       7.3 Area Nodes For Triggers  
       7.4 The _physics_process Loop  
   8. **[UI With Control Nodes](./[8]-UI-With-Control-Nodes.md)**  
       8.1 The Control Node Family  
       8.2 Anchors, Margins, And Containers  
       8.3 Building A Simple Menu  
       8.4 Theming UI  

**Shipping**
   9. **[Exporting And Publishing A Godot Project](./[9]-Exporting-And-Publishing-A-Godot-Project.md)**  
       9.1 Export Templates  
       9.2 Export Presets For Different Platforms  
       9.3 Project Settings Before Export  
       9.4 Where To Publish (itch.io, Steam, App Stores)  
