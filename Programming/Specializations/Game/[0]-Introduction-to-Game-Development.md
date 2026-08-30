[⬅ Back to README](../../../README.md)

# Game Development

Welcome! This is a self-paced course for learning Game Development, the practice of designing and building interactive games using modern engines, from small 2D prototypes to full 3D, multiplayer experiences.

---

## What is Game Development?

Game Development lets you:
- Turn an idea into a design document and a playable prototype
- Build 2D and 3D games using industry-standard engines like Unity, Unreal, and Godot
- Design levels, scenes, and game worlds, including procedurally generated ones
- Program gameplay logic, player controls, and AI behavior
- Work with physics, collisions, and animation systems
- Create UI, HUDs, and menus for a polished player experience
- Add sound, music, and visual effects
- Build multiplayer experiences that sync state across a network
- Manage assets and versioned projects with the right tooling
- Optimize, test, localize, and ship a game to platforms like Steam, the App Store, and Google Play
- Understand how monetization and player analytics fit into a shipped game

## Table of Contents

**Getting Started**  
    1. **[What is Game Development? Genres & Engines Overview](./[1]-What-is-Game-Development.md)**  
       1.1 What Is Game Development?  
       1.2 Common Game Genres  
       1.3 Popular Game Engines (Unity, Unreal, Godot)  
       1.4 Choosing the Right Engine for Your Project  
    2. **[Setting Up a Game Engine (Unity, Unreal, Godot)](./[2]-Setting-Up-a-Game-Engine.md)**  
       2.1 System Requirements & Installation  
       2.2 Installing Unity via Unity Hub  
       2.3 Installing Unreal Engine via Epic Games Launcher  
       2.4 Installing Godot  
       2.5 Creating Your First Project  
    3. **[Anatomy of a Game Project](./[3]-Anatomy-of-a-Game-Project.md)**  
       3.1 The Project Folder Structure  
       3.2 Assets, Scenes, and Scripts  
       3.3 Build Settings and Configuration Files  
       3.4 Common Conventions Across Engines  
    4. **[Prototyping & Game Design Documents](./[4]-Prototyping-and-Game-Design-Documents.md)**  
       4.1 Why Prototype First?  
       4.2 What Is a Game Design Document (GDD)?  
       4.3 Core Sections of a GDD  
       4.4 From Paper Prototype to Digital Prototype  

**Core Programming Concepts**  
    5. **[Variables, Data Types & Operators](./%5B5%5D-Variables-and-Data-Types%20%281%29.md)**  
       5.1 What Is a Variable?  
       5.2 Common Data Types  
       5.3 Operators  
       5.4 Naming Conventions for Game Code  
    6. **[Control Flow: Conditionals & Loops](./%5B6%5D-Control-Flow%20%281%29.md)**  
       6.1 Conditional Statements  
       6.2 Loops  
       6.3 Using Control Flow in Gameplay Code  
    7. **[Functions & Scope](./%5B7%5D-Functions-and-Scope%20%281%29.md)**  
       7.1 What Is a Function?  
       7.2 Parameters and Return Values  
       7.3 Scope: Local vs Global  
       7.4 Functions in Game Engine Callbacks  
    8. **[Object-Oriented Basics for Games (Classes, Components)](./%5B8%5D-Object-Oriented-Basics%20%281%29.md)**  
       8.1 Classes and Objects  
       8.2 Inheritance  
       8.3 Components and Composition  
       8.4 Composition over Inheritance in Game Engines  
    9. **[Collections & Data Structures for Games](./%5B9%5D-Collections-and-Data-Structures%20%281%29.md)**  
       9.1 Arrays and Lists  
       9.2 Dictionaries / Maps  
       9.3 Queues and Stacks  
       9.4 Choosing the Right Structure  

**Game Engine Fundamentals**  
    10. **[The Game Loop](./[10]-The-Game-Loop.md)**  
        10.1 What Is a Game Loop?  
        10.2 Update, Render, and Fixed Update  
        10.3 Delta Time  
        10.4 Frame Rate and Frame Independence  
    11. **[Scenes, GameObjects & Entities](./[11]-Scenes-GameObjects-and-Entities.md)**  
        11.1 What Is a Scene?  
        11.2 GameObjects / Entities / Nodes  
        11.3 Parent-Child Hierarchies  
        11.4 Instantiating and Destroying Objects  
    12. **[Components & Component-Based Architecture](./[12]-Component-Based-Architecture.md)**  
        12.1 What Is a Component?  
        12.2 Entity-Component Pattern  
        12.3 Entity-Component-System (ECS)  
        12.4 Building Behavior from Components  
    13. **[Transforms, Coordinates & Vectors](./[13]-Transforms-Coordinates-and-Vectors.md)**  
        13.1 What Is a Transform?  
        13.2 Coordinate Systems  
        13.3 Vectors  
        13.4 Local vs World Space  

**2D Game Development**  
    14. **[Sprites & Spritesheets](./[14]-Sprites-and-Spritesheets.md)**  
        14.1 What Is a Sprite?  
        14.2 Spritesheets and Atlases  
        14.3 Sprite Rendering Properties  
        14.4 Frame-Based Animation Basics  
    15. **[Tilemaps & Level Design](./[15]-Tilemaps-and-Level-Design.md)**  
        15.1 What Is a Tilemap?  
        15.2 Tilesets and Tile Palettes  
        15.3 Layers  
        15.4 Designing Levels with Tiles  
    16. **[2D Physics & Collisions](./[16]-2D-Physics-and-Collisions.md)**  
        16.1 Physics Bodies in 2D  
        16.2 Colliders  
        16.3 Collision Detection vs Collision Response  
        16.4 Triggers vs Solid Collisions  
    17. **[Cameras in 2D](./[17]-Cameras-in-2D.md)**  
        17.1 The 2D Camera  
        17.2 Camera Following  
        17.3 Camera Bounds  
        17.4 Zoom and Parallax  

**3D Game Development**  
    18. **[3D Models, Meshes & Materials](./[18]-3D-Models-Meshes-and-Materials.md)**  
        18.1 Meshes and Vertices  
        18.2 Materials and Shaders (Overview)  
        18.3 Textures and UV Mapping  
        18.4 Importing 3D Models  
    19. **[Lighting & Shading Basics](./[19]-Lighting-and-Shading-Basics.md)**  
        19.1 Types of Lights  
        19.2 Shading Models  
        19.3 Baked vs Real-Time Lighting  
        19.4 Shadows  
    20. **[3D Physics & Collisions](./[20]-3D-Physics-and-Collisions.md)**  
        20.1 Rigidbodies in 3D  
        20.2 Colliders in 3D  
        20.3 Forces and Gravity  
        20.4 Raycasting  
    21. **[Cameras in 3D](./[21]-Cameras-in-3D.md)**  
        21.1 Perspective vs Orthographic  
        21.2 Camera Rigs (First-Person, Third-Person)  
        21.3 Field of View and Clipping Planes  
        21.4 Camera Collision  

**Gameplay Systems**  
    22. **[Player Input & Controls](./[22]-Player-Input-and-Controls.md)**  
        22.1 Input Devices  
        22.2 Polling vs Event-Driven Input  
        22.3 Input Mapping / Action Systems  
        22.4 Handling Multiple Input Sources  
    23. **[Animation Systems](./[23]-Animation-Systems.md)**  
        23.1 Keyframe Animation  
        23.2 Animation State Machines  
        23.3 Blending and Transitions  
        23.4 Rigging and Skeletal Animation (Overview)  
    24. **[Character Movement & Controllers](./[24]-Character-Movement-and-Controllers.md)**  
        24.1 Kinematic vs Physics-Based Movement  
        24.2 Ground Detection  
        24.3 Jumping and Gravity Tuning  
        24.4 Character Controllers in Engines  
    25. **[AI & NPC Behavior Basics](./[25]-AI-and-NPC-Behavior-Basics.md)**  
        25.1 What Is Game AI?  
        25.2 Finite State Machines  
        25.3 Behavior Trees (Overview)  
        25.4 Simple Decision Making  
    26. **[Pathfinding](./[26]-Pathfinding.md)**  
        26.1 What Is Pathfinding?  
        26.2 Navigation Meshes (NavMesh)  
        26.3 The A* Algorithm (Conceptually)  
        26.4 Dynamic Obstacles  

**Game Systems & Logic**  
    27. **[Game State Management](./[27]-Game-State-Management.md)**  
        27.1 What Is Game State?  
        27.2 State Machines for Game Flow  
        27.3 Global vs Scene-Level State  
        27.4 Persisting State Across Scenes  
    28. **[Inventory & Item Systems](./[28]-Inventory-and-Item-Systems.md)**  
        28.1 Modeling Items  
        28.2 Inventory Data Structures  
        28.3 Stacking, Equipping, and Using Items  
        28.4 UI Considerations  
    29. **[Dialogue & Quest Systems](./[29]-Dialogue-and-Quest-Systems.md)**  
        29.1 Dialogue Trees  
        29.2 Quest States and Objectives  
        29.3 Triggers and Conditions  
        29.4 Data-Driven Dialogue  
    30. **[Save & Load Systems](./[30]-Save-and-Load-Systems.md)**  
        30.1 What Needs to Be Saved?  
        30.2 Serialization Formats  
        30.3 Save Files and Slots  
        30.4 Common Pitfalls  
    31. **[Procedural Generation](./[31]-Procedural-Generation.md)**  
        31.1 What Is Procedural Generation?  
        31.2 Random Number Generation and Seeds  
        31.3 Noise Functions  
        31.4 Procedural Levels and Content  

**Audio & UX**  
    32. **[Sound Effects & Music](./[32]-Sound-Effects-and-Music.md)**  
        32.1 Audio Sources and Listeners  
        32.2 SFX vs Music vs Ambience  
        32.3 3D Spatial Audio  
        32.4 Audio Mixing Basics  
    33. **[UI/UX for Games (HUD, Menus)](./[33]-UI-UX-for-Games.md)**  
        33.1 HUD vs Menus  
        33.2 Canvas and UI Systems  
        33.3 Responsive and Scalable UI  
        33.4 UX Principles for Games  
    34. **[Particle Effects & Visual Feedback](./[34]-Particle-Effects-and-Visual-Feedback.md)**  
        34.1 What Are Particle Systems?  
        34.2 Particle Properties  
        34.3 Common Effects  
        34.4 Feedback and Game Feel  

**Multiplayer & Networking**  
    35. **[Introduction to Multiplayer Concepts](./[35]-Introduction-to-Multiplayer-Concepts.md)**  
        35.1 Why Multiplayer Is Different  
        35.2 Common Multiplayer Models  
        35.3 Core Terminology  
        35.4 Challenges Unique to Multiplayer  
    36. **[Client-Server Models for Games](./[36]-Client-Server-Models-for-Games.md)**  
        36.1 Peer-to-Peer vs. Client-Server  
        36.2 Dedicated Server vs. Listen Server  
        36.3 Server Authority  
        36.4 Matchmaking and Lobbies  
    37. **[Syncing Game State Over a Network](./[37]-Syncing-Game-State-Over-a-Network.md)**  
        37.1 Network Ticks and Snapshots  
        37.2 State Sync vs. Input Sync  
        37.3 Client-Side Prediction and Reconciliation  
        37.4 Interpolation and Lag Compensation  

**Tooling & Production**  
    38. **[Version Control for Game Projects (Git, Git LFS)](./[38]-Version-Control-for-Game-Projects.md)**  
        38.1 Why Version Control Matters for Games  
        38.2 Git Basics Recap  
        38.3 The Binary File Problem and Git LFS  
        38.4 .gitignore and Team Workflows  
    39. **[Asset Pipelines & Import Workflows](./[39]-Asset-Pipelines-and-Import-Workflows.md)**  
        39.1 What Is an Asset Pipeline?  
        39.2 Import Settings  
        39.3 Naming Conventions and Folder Organization  
        39.4 Automating the Pipeline  

**Polishing & Shipping**  
    40. **[Performance Optimization for Games](./[40]-Performance-Optimization-for-Games.md)**  
        40.1 Profiling Before Optimizing  
        40.2 CPU vs. GPU Bottlenecks  
        40.3 Common Optimization Techniques  
        40.4 Memory Management  
    41. **[Testing & Debugging Games](./[41]-Testing-and-Debugging-Games.md)**  
        41.1 Types of Testing  
        41.2 In-Engine Debugging Tools  
        41.3 Logging  
        41.4 Common Bug Categories in Games  
    42. **[Building & Exporting a Game](./[42]-Building-and-Exporting-a-Game.md)**  
        42.1 What a Build Is  
        42.2 Debug vs. Release Builds  
        42.3 Platform-Specific Build Settings  
        42.4 Build Automation  
    43. **[Publishing to App Stores & Platforms (Steam, App Store, Google Play)](./[43]-Publishing-to-Platforms.md)**  
        43.1 Platform Requirements and Certification  
        43.2 Store Pages and Metadata  
        43.3 Platform-Specific Considerations  
        43.4 Post-Launch Updates and Patching  

**Best Practices**  
    44. **[Game Design Principles](./[44]-Game-Design-Principles.md)**  
        44.1 The Core Loop  
        44.2 Player Agency and Feedback  
        44.3 Difficulty and Balance  
        44.4 Player Motivation  
    45. **[Accessibility in Games](./[45]-Accessibility-in-Games.md)**  
        45.1 Why Accessibility Matters  
        45.2 Visual Accessibility  
        45.3 Motor and Input Accessibility  
        45.4 Cognitive and Audio Accessibility  
    46. **[Localization for Games](./[46]-Localization-for-Games.md)**  
        46.1 What Localization Involves  
        46.2 Internationalization vs. Localization  
        46.3 Text Expansion and UI Considerations  
        46.4 Culturalization  
    47. **[Monetization Basics (Ads, IAP, Premium)](./[47]-Monetization-Basics.md)**  
        47.1 Common Monetization Models  
        47.2 In-App Purchases  
        47.3 Advertising  
        47.4 Ethical Considerations  
    48. **[Analytics & Player Telemetry](./[48]-Analytics-and-Player-Telemetry.md)**
        48.1 What Is Telemetry?  
        48.2 Common Metrics  
        48.3 Implementing Analytics  
        48.4 Privacy Considerations  