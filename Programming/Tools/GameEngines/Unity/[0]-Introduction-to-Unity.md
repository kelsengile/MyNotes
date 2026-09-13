[⬅ Back to Game Engine Fundamentals](../[0]-Introduction-to-GameEngines.md)

# Introduction to Unity

Unity is a widely-used, general-purpose game engine built around a **GameObject + Component** architecture, where behavior is assembled by attaching small, reusable components to objects in a scene rather than writing one large class per object. It uses **C#** as its scripting language, supports both 2D and 3D development, and exports to more platforms than almost any other engine — desktop, mobile, console, and web among them.

This Topic takes the tool-agnostic ideas from Game Engine Fundamentals — scenes, transforms, rendering, physics, scripting — and grounds them in Unity's specific editor, terminology, and workflow. By the end, you should be comfortable navigating the Unity Editor, building GameObjects out of components, scripting behavior in C#, and building a project for a target platform.

## Why Learn Unity?

- **Huge learning resources and community** — Unity has one of the largest communities of any engine, meaning tutorials, forum answers, and third-party assets exist for almost any problem you'll run into.
- **Broad platform reach** — a single Unity project can target Windows, macOS, Linux, iOS, Android, consoles, and WebGL, often with only minor per-platform adjustments.
- **Gentle learning curve** — the component-based workflow and visual Inspector make it easy to see and tweak how a GameObject behaves without diving straight into code.
- **The Asset Store** — a large marketplace of ready-made models, tools, and systems that can dramatically speed up development, especially for solo developers and small teams.
- **Strong for both 2D and 3D** — unlike some engines that favor one or the other, Unity has mature, first-party tooling for both.

Download: [docs.unity.com/en-us/hub/install-hub](https://docs.unity.com/en-us/hub/install-hub)

## Table of Contents

**Getting Started**
   1. **[Installing Unity And Unity Hub](./[1]-Installing-Unity-And-Unity-Hub.md)**  
       1.1 Unity Hub And Managing Editor Versions  
       1.2 LTS vs Tech Stream Releases  
       1.3 Creating A New Project And Templates  
       1.4 Unity License Tiers  
   2. **[The Unity Editor Interface](./[2]-The-Unity-Editor-Interface.md)**  
       2.1 Scene View vs Game View  
       2.2 The Hierarchy And Project Windows  
       2.3 The Inspector  
       2.4 Layouts And Customization  

**Core Concepts And Scripting**
   3. **[GameObjects, Components, And Prefabs In Unity](./[3]-GameObjects,-Components,-And-Prefabs-In-Unity.md)**  
       3.1 GameObjects As Containers  
       3.2 Adding And Configuring Components  
       3.3 Prefabs And Prefab Variants  
       3.4 Instantiating Prefabs At Runtime  
   4. **[Introduction To C# Scripting In Unity](./[4]-Introduction-To-C%23-Scripting-In-Unity.md)**  
       4.1 Creating A MonoBehaviour Script  
       4.2 Public Fields And The Inspector  
       4.3 Attaching Scripts To GameObjects  
       4.4 Accessing Other Components (GetComponent)  
   5. **[MonoBehaviour Lifecycle Methods](./[5]-MonoBehaviour-Lifecycle-Methods.md)**  
       5.1 Awake vs Start  
       5.2 Update, FixedUpdate, And LateUpdate  
       5.3 OnEnable/OnDisable And OnDestroy  
       5.4 Coroutines  

**Building Features**
   6. **[Unity Physics And Colliders](./[6]-Unity-Physics-And-Colliders.md)**  
       6.1 Rigidbody And Rigidbody2D  
       6.2 Colliders And Collision Detection Modes  
       6.3 OnCollisionEnter vs OnTriggerEnter  
       6.4 Physics Materials In Unity  
   7. **[The Unity UI System (Canvas And RectTransform)](./[7]-The-Unity-UI-System-(Canvas-And-RectTransform).md)**  
       7.1 The Canvas And Render Modes  
       7.2 RectTransform And Anchoring  
       7.3 UI Components (Button, Text, Image, Slider)  
       7.4 The New UI Toolkit vs UGUI  
   8. **[Unity's Animation System (Animator And Mecanim)](./[8]-Unity's-Animation-System-(Animator-And-Mecanim).md)**  
       8.1 Animation Clips  
       8.2 The Animator Controller And State Machines  
       8.3 Blend Trees  
       8.4 Animation Events  

**Shipping**
   9. **[Building And Exporting A Unity Project](./[9]-Building-And-Exporting-A-Unity-Project.md)**  
       9.1 Build Settings And Target Platforms  
       9.2 Player Settings  
       9.3 Addressables And Asset Bundles  
       9.4 Publishing To Stores  
