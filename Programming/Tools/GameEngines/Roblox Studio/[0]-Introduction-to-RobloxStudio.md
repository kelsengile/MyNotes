[⬅ Back to Game Engine Fundamentals](../[0]-Introduction-to-GameEngines.md)

# Introduction to Roblox Studio

Roblox Studio is the development environment used to build, script, test, and publish experiences on the Roblox platform. Unlike a general-purpose engine you export a standalone game from, Roblox Studio is tied to a specific platform: everything you build runs inside the Roblox client, is scripted in **Luau** (Roblox's typed dialect of Lua), and is instantly reachable by Roblox's existing audience of players once published.

This Topic grounds the tool-agnostic ideas from Game Engine Fundamentals in Roblox's specific vocabulary and workflow — Instances instead of GameObjects, Services instead of engine subsystems, and a client-server model that shapes how every script you write has to be structured. By the end, you should be able to build a simple place, script interactive behavior safely, and publish an experience.

## Why Learn Roblox Studio?

- **Built-in audience and platform** — a published experience is immediately discoverable by Roblox's massive existing player base, with no separate storefront or install step needed.
- **Fast to start building** — Studio ships with drag-and-drop building tools, a large asset Toolbox, and templates that let you get something playable in minutes.
- **Multiplayer by default** — the client-server networking model is built into the platform itself, so multiplayer is the norm rather than something you bolt on afterward.
- **Approachable scripting language** — Luau is a friendly entry point into programming, with real-time feedback as you test your experience.
- **Real monetization path** — Roblox has an established in-platform economy (Robux, Game Passes, Developer Products) that lets creators earn from their experiences.

Download: [create.roblox.com/docs/studio/setup](https://create.roblox.com/docs/studio/setup)

## Table of Contents

**Getting Started**

   1. **[Getting Started With Roblox Studio](./[1]-Getting-Started-With-Roblox-Studio.md)**  
       1.1 Creating A Roblox Account And Installing Studio  
       1.2 Templates And Starting A New Place  
       1.3 Roblox Terminology (Experience, Place, Game)  
       1.4 Studio vs The Roblox Player App  
   2. **[The Roblox Studio Interface](./[2]-The-Roblox-Studio-Interface.md)**  
       2.1 The Explorer And Properties Panels  
       2.2 The 3D Viewport And Navigation  
       2.3 The Toolbox  
       2.4 Test Mode (Play, Run, Server/Client)  

**Building And Scripting**

   3. **[The Roblox Instance Hierarchy](./[3]-The-Roblox-Instance-Hierarchy.md)**  
       3.1 Instances As Roblox's Building Block  
       3.2 Key Services (Workspace, Players, ReplicatedStorage, ServerScriptService)  
       3.3 Parent-Child Relationships In The Explorer  
       3.4 Finding Instances In Code  
   4. **[Introduction To Luau Scripting](./[4]-Introduction-To-Luau-Scripting.md)**  
       4.1 Luau vs Standard Lua  
       4.2 Variables, Types, And Control Flow  
       4.3 Functions And Tables  
       4.4 The Script Editor And Output Window  
   5. **[Server Scripts, Local Scripts, And RemoteEvents](./[5]-Server-Scripts,-Local-Scripts,-And-RemoteEvents.md)**  
       5.1 The Client-Server Model In Roblox  
       5.2 Script vs LocalScript vs ModuleScript  
       5.3 RemoteEvents And RemoteFunctions  
       5.4 Security: Why You Never Trust The Client  

**Advanced Experience Design**

   6. **[Building Parts, Models, And Terrain](./[6]-Building-Parts,-Models,-And-Terrain.md)**  
       6.1 Parts And Basic Building Tools  
       6.2 Grouping Parts Into Models  
       6.3 The Terrain Editor  
       6.4 Meshes And Imported Assets  
   7. **[Roblox Physics And Constraints](./[7]-Roblox-Physics-And-Constraints.md)**  
       7.1 Anchored vs Unanchored Parts  
       7.2 Constraints (Hinge, Spring, Weld, Motor)  
       7.3 Collision Groups  
       7.4 CFrame And Vector3 Basics  
   8. **[GUI With Roblox UI Elements](./[8]-GUI-With-Roblox-UI-Elements.md)**  
       8.1 ScreenGui And Its Children  
       8.2 Frames, TextLabels, And Buttons  
       8.3 UDim2 And Scaling For Different Screens  
       8.4 Connecting UI To Scripts  

**Publishing**

   9. **[Publishing And Monetizing A Roblox Experience](./[9]-Publishing-And-Monetizing-A-Roblox-Experience.md)**  
       9.1 Publishing A Place To Roblox  
       9.2 Configuring Experience Settings And Access  
       9.3 Monetization (Game Passes, Developer Products)  
       9.4 Community Guidelines And Moderation  
