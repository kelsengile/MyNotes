[⬅ Back to README](../../../README.md)

# Introduction to Game Engines

A game engine is a software framework that gives developers the tools to build games without reinventing the wheel every time — rendering, physics, input, audio, and scripting are all provided as reusable systems that a team builds their game logic on top of. This Topic builds up the fundamental, tool-agnostic concepts behind game engines from the ground up: what an engine actually is, how it organizes a game world, how it renders and simulates that world, and how real games are built, optimized, and shipped using one.

These lessons intentionally avoid tying the concepts to any single engine. Once you understand the fundamentals here, you'll be equipped to pick up Unity, Unreal, Godot, or any other engine and recognize the same ideas under different names and menus.

## Why Study Game Engines?

- **You skip reinventing the basics** — rendering, physics, and input handling are hard problems that engines have already solved, letting you focus on the game itself.
- **The concepts transfer between engines** — scene graphs, components, and game loops appear under different names in Unity, Unreal, and Godot alike, so learning one deeply makes the next one easier.
- **Game engines are used beyond games** — architecture walkthroughs, training simulations, and film previsualization all run on the same engine concepts you'll learn here.
- **It's hands-on and visual** — few areas of software let you see the direct result of a concept (like a physics tweak or a lighting change) as immediately as a game engine does.

A quick look at how some popular engines differ, which you'll explore in depth later:

| Engine | Primary Language | Known For | Best Fit For |
|---|---|---|---|
| Unity | C# | Flexibility, huge asset store | 2D and 3D, mobile and indie games |
| Unreal Engine | C++ / Blueprints | High-fidelity 3D graphics | AAA and visually demanding games |
| Godot | GDScript / C# | Lightweight, open-source | Indie and 2D-focused projects |
| Roblox Studio | Lua | Built-in platform and audience | Social and multiplayer experiences |

## Game Engines

The lessons above introduce the fundamentals of game development and interactive applications. To see those concepts applied to specific, industry-standard game engines and development platforms, continue on to:

* **[Godot](https://godotengine.org/download/)** — a free and open-source game engine known for its lightweight workflow and flexible 2D and 3D development capabilities.

* **[Roblox Studio](https://create.roblox.com/docs/studio/setup)** — Roblox's development environment for creating, scripting, testing, and publishing interactive experiences on the Roblox platform.

* **[Unity](https://docs.unity.com/en-us/hub/install-hub)** — a widely-used game engine supporting 2D and 3D development across multiple platforms.

* **[Unreal Engine](https://www.unrealengine.com/download)** — a powerful game engine known for high-fidelity 3D graphics, advanced rendering, and large-scale game development.


## Table of Contents

**Engine Foundations**
   1. **[What Is a Game Engine?](./[1]-What-Is-A-Game-Engine.md)**  
       1.1 Defining a Game Engine  
       1.2 Engines vs Frameworks vs Libraries  
       1.3 Why Game Engines Exist  
       1.4 Key Benefits  
   2. **[Core Engine Architecture](./[2]-Core-Engine-Architecture.md)**  
       2.1 The Game Loop  
       2.2 Engine Subsystems Overview  
       2.3 Runtime vs Editor  
       2.4 Data-Driven Design  
   3. **[Popular Game Engines](./[3]-Popular-Game-Engines.md)**  
       3.1 Unity  
       3.2 Unreal Engine  
       3.3 Godot  
       3.4 Custom and Proprietary Engines  

**World And Object Management**

   4. **[Scenes And The Game World](./[4]-Scenes-And-The-Game-World.md)**  
       4.1 What Is a Scene  
       4.2 Scene Graphs  
       4.3 Loading And Unloading Scenes  
       4.4 A Simple Scene Example  
   5. **[GameObjects And Components](./[5]-GameObjects-And-Components.md)**  
       5.1 The GameObject Concept  
       5.2 Component-Based Architecture  
       5.3 Entity-Component-System (ECS)  
       5.4 Composition Over Inheritance  
   6. **[Transforms And Spatial Hierarchy](./[6]-Transforms-And-Spatial-Hierarchy.md)**  
       6.1 Position, Rotation, and Scale  
       6.2 Parent-Child Hierarchies  
       6.3 Local Space vs World Space  
       6.4 Transform Matrices  

**Rendering**

   7. **[Introduction To Rendering](./[7]-Introduction-To-Rendering.md)**  
       7.1 What Is a Renderer  
       7.2 The Rendering Pipeline  
       7.3 Cameras And Viewports  
       7.4 Frame Rate And The Render Loop  
   8. **[Materials, Shaders, And Lighting](./[8]-Materials-Shaders-And-Lighting.md)**  
       8.1 Materials And Textures  
       8.2 What Is a Shader  
       8.3 Lighting Models  
       8.4 Shadows  
   9. **[2D Vs 3D Rendering](./[9]-2D-Vs-3D-Rendering.md)**  
       9.1 Sprites And 2D Rendering  
       9.2 Meshes And 3D Rendering  
       9.3 Mixing 2D And 3D  
       9.4 Choosing a Rendering Approach  

**Physics And Interaction**

   10. **[Physics Engines](./[10]-Physics-Engines.md)**  
       10.1 What Is a Physics Engine  
       10.2 Rigidbodies And Colliders  
       10.3 Forces And Gravity  
       10.4 Physics Materials  
   11. **[Collision Detection And Response](./[11]-Collision-Detection-And-Response.md)**  
       11.1 Broad Phase vs Narrow Phase  
       11.2 Collision Shapes  
       11.3 Triggers vs Collisions  
       11.4 Collision Events  
   12. **[Input Handling](./[12]-Input-Handling.md)**  
       12.1 Input Devices  
       12.2 Polling vs Event-Driven Input  
       12.3 Input Mapping And Action Systems  
       12.4 Handling Multiple Players  

**Gameplay Systems**

   13. **[Scripting And Game Logic](./[13]-Scripting-And-Game-Logic.md)**  
       13.1 Scripting Languages In Engines  
       13.2 Lifecycle Methods  
       13.3 Event Systems And Messaging  
       13.4 State Machines  
   14. **[Animation Systems](./[14]-Animation-Systems.md)**  
       14.1 Keyframe Animation  
       14.2 Skeletal Animation And Rigging  
       14.3 Animation Blending And Transitions  
       14.4 Animation State Machines  
   15. **[Audio Systems](./[15]-Audio-Systems.md)**  
       15.1 Sound Effects vs Music  
       15.2 Audio Sources And Listeners  
       15.3 3D Spatial Audio  
       15.4 Mixing And Audio Buses  

**Performance And Production**

   16. **[Asset Pipelines And Resource Management](./[16]-Asset-Pipelines-And-Resource-Management.md)**  
       16.1 Importing Assets  
       16.2 Asset Loading Strategies  
       16.3 Memory Management  
       16.4 Object Pooling  
   17. **[Optimization And Profiling](./[17]-Optimization-And-Profiling.md)**  
       17.1 Why Optimization Matters  
       17.2 Profiling Tools  
       17.3 Common Performance Bottlenecks  
       17.4 Level Of Detail (LOD)  
   18. **[Building And Deploying Games](./[18]-Building-And-Deploying-Games.md)**  
       18.1 Build Targets And Platforms  
       18.2 Build Configurations  
       18.3 Packaging Assets  
       18.4 Testing Across Platforms  

**Next Steps**

   19. **[Choosing A Game Engine](./[19]-Choosing-A-Game-Engine.md)**  
       19.1 Matching Engines To Project Needs  
       19.2 2D-Focused vs 3D-Focused Engines  
       19.3 Learning Curve And Community  
       19.4 Where To Go Next  