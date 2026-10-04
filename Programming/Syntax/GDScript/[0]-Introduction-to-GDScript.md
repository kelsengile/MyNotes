[⬅ Back to README](../../../README.md)

<div align="center">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/godot/godot-original.svg" width="50"/><br>
  <h1 style="margin-top: 0;">GDScript</h1>
</div>


Welcome! This is a self-paced course for learning GDScript, the Python-like scripting language built into the Godot Engine. It is designed for making 2D and 3D games, tools, and interactive apps, and it is tightly integrated with Godot's node and scene system. This course targets **Godot 4.x**.

Download: [https://godotengine.org/download/](https://godotengine.org/download/)

---

## What is GDScript?

GDScript lets you:
- Write clean, readable code with an indentation-based syntax similar to Python
- Control game objects (nodes) and respond to events such as input, collisions, and timers
- Build 2D and 3D games, from small prototypes to full releases
- Use optional static typing for safer, faster, and better auto-completed code
- Connect game logic with signals instead of tight coupling
- Create reusable scenes, custom resources, and editor tools
- Handle input from keyboard, mouse, touch, and gamepads
- Animate, tween, and play audio, and build user interfaces
- Save and load game data, and add networking and multiplayer
- Export to Windows, macOS, Linux, Android, iOS, and the Web

## Table of Contents

**Getting Started**  

1. **[Installing Godot & First-Time Setup](./[1]-Installation-and-Setup.md)**  
    1.1 What You Need Before You Start  
    1.2 Downloading Godot (Standard vs .NET version)  
    1.3 Installing on Windows, macOS, and Linux  
    1.4 The Project Manager  
    1.5 Creating Your First Project  
    1.6 Choosing a Renderer (Forward+, Mobile, Compatibility)  
    1.7 Using an External Editor (VS Code, etc.)  
2. **[The Godot Editor Tour](./[2]-Editor-Tour.md)**  
    2.1 The Main Workspaces (2D, 3D, Script, AssetLib)  
    2.2 The Scene Dock and Node Dock  
    2.3 The Inspector  
    2.4 The FileSystem Dock  
    2.5 The Script Editor and Built-in Docs  
    2.6 The Output, Debugger, and Animation Panels  
3. **[Running Code: Scripts, Scenes & the Output Panel](./[3]-Running-GDScript-Code.md)**  
    3.1 How GDScript Fits into Godot  
    3.2 Creating and Attaching a Script to a Node  
    3.3 Your First Script: `print("Hello, World!")`  
    3.4 Running the Project, the Current Scene, and the Main Scene  
    3.5 Running a Script Directly with `@tool` and `EditorScript`  
    3.6 Reading Output, Warnings, and Errors  
4. **[Project Structure & Settings](./[4]-Project-Structure-and-Settings.md)**  
    4.1 `res://` and `user://` Paths  
    4.2 The `project.godot` File  
    4.3 Organizing Folders, Scenes, and Scripts  
    4.4 Project Settings (Display, Input Map, Layers, Autoloads)  
    4.5 Importing Assets (Images, Audio, 3D Models)  
    4.6 Version Control with Git (`.gitignore`, `.godot/` folder)  

**Core Syntax**  

5. **[Variables, Constants & Data Types](./[5]-Variables-and-Data-Types.md)**  
    5.1 What is a Variable? (`var`)  
    5.2 Naming Rules & Conventions (`snake_case`)  
    5.3 Constants (`const`)  
    5.4 Dynamic vs Static Typing  
    5.5 Basic Data Types Overview  
    5.6 Type Inference with `:=`  
    5.7 Type Checking with `typeof()` and `is`  
    5.8 Type Casting/Conversion (`as`, `int()`, `float()`, `str()`)  
    5.9 Comments and Documentation Comments (`##`)  
6. **[Numbers, Strings & Booleans](./[6]-Numbers-Strings-and-Booleans.md)**  
    6.1 Integers and Floats  
    6.2 Arithmetic with Numbers and Built-in Math Functions  
    6.3 Strings: Creation and Basics  
    6.4 `String`, `StringName`, and `NodePath`  
    6.5 Booleans and Truthiness  
    6.6 `null` and the `Variant` Concept  
7. **[Operators & Expressions](./[7]-Operators-and-Expressions.md)**  
    7.1 Arithmetic Operators  
    7.2 Comparison Operators  
    7.3 Logical Operators (`and`, `or`, `not`, `&&`, `||`, `!`)  
    7.4 Assignment Operators  
    7.5 Bitwise Operators  
    7.6 Membership Operator (`in`)  
    7.7 Type Operators (`is`, `as`)  
    7.8 The Ternary Expression  
    7.9 Operator Precedence  
8. **[Conditionals: if, elif, else, match](./[8]-Conditionals.md)**  
    8.1 The `if` Statement  
    8.2 `elif` and `else`  
    8.3 Nested Conditionals  
    8.4 Conditional (Ternary) Expressions  
    8.5 Truthy and Falsy Values in Conditions  
    8.6 The `match` Statement (Pattern Matching)  
    8.7 Match Patterns: Constants, Variables, Arrays, Dictionaries, Wildcards, Binding  
9. **[Loops: for & while, break, continue, pass](./[9]-Loops.md)**  
    9.1 The `for` Loop  
    9.2 The `range()` Function  
    9.3 Looping Over Arrays, Dictionaries, and Strings  
    9.4 The `while` Loop  
    9.5 `break`, `continue`, and `pass`  
    9.6 Nested Loops  
    9.7 Why Loops Don't Replace `_process()`  
10. **[Functions & Scope](./[10]-Functions-and-Scope.md)**  
    10.1 Defining and Calling Functions (`func`)  
    10.2 Parameters, Arguments & Return Values  
    10.3 Default Arguments  
    10.4 Typed Parameters and Return Types (`-> int`)  
    10.5 Returning Multiple Values (Arrays/Dictionaries)  
    10.6 Variable-Argument Workarounds  
    10.7 Local, Member, and Global Scope  
    10.8 Recursion  
11. **[String Formatting & Manipulation](./[11]-String-Formatting.md)**  
    11.1 The `%` Format Operator  
    11.2 The `.format()` Method  
    11.3 Converting with `str()` and `String.num()`  
    11.4 String Slicing and Indexing  
    11.5 Common String Methods (`split`, `join`, `replace`, `find`, `to_upper`, `strip_edges`)  
    11.6 Multiline Strings and Escape Characters  
    11.7 Raw Strings and `tr()` for Translation  
12. **[Error Handling & Defensive Coding](./[12]-Error-Handling.md)**  
    12.1 How GDScript Handles Errors (No try/except)  
    12.2 `assert()`  
    12.3 `push_error()` and `push_warning()`  
    12.4 Checking for `null` and `is_instance_valid()`  
    12.5 Error Codes and the `Error` Enum  
    12.6 Validating Input and Return Values  
    12.7 Common Beginner Errors and How to Read Them  

**Data Structures**  

13. **[Arrays](./[13]-Arrays.md)**  
    13.1 Arrays: Ordered & Mutable  
    13.2 Indexing, Negative Indexes, and Slicing  
    13.3 Common Array Methods (`append`, `insert`, `erase`, `pop_back`, `sort`, `find`, `map`, `filter`, `reduce`)  
    13.4 Typed Arrays (`Array[int]`)  
    13.5 Multidimensional Arrays and Grids  
    13.6 Packed Arrays (`PackedInt32Array`, `PackedVector2Array`, etc.)  
    13.7 Copying Arrays: Shallow vs Deep (`duplicate()`)  
14. **[Dictionaries](./[14]-Dictionaries.md)**  
    14.1 What is a Dictionary?  
    14.2 Creating and Accessing Dictionaries (including Lua-style syntax)  
    14.3 Adding, Updating, Removing Items  
    14.4 Iterating Over Dictionaries  
    14.5 Dictionary Methods (`keys`, `values`, `has`, `get`, `merge`)  
    14.6 Nested Dictionaries  
    14.7 Typed Dictionaries  
    14.8 Dictionaries as Lightweight Records  
15. **[Enums & Constants](./[15]-Enums-and-Constants.md)**  
    15.1 Defining Enums  
    15.2 Named vs Anonymous Enums  
    15.3 Using Enums with `match`  
    15.4 Enums as Exported Dropdowns  
    15.5 Global Constants and `preload()` Constants  
16. **[Built-in Math Types: Vectors, Colors, Rects & Transforms](./[16]-Built-in-Math-Types.md)**  
    16.1 `Vector2` and `Vector2i`  
    16.2 `Vector3` and `Vector3i`  
    16.3 Vector Math: Length, Normalize, Dot, Cross, Distance, Direction  
    16.4 `Color`  
    16.5 `Rect2` and `AABB`  
    16.6 `Transform2D`, `Transform3D`, and `Basis`  
    16.7 `Quaternion` and Rotations  
17. **[Static Typing & the Variant System](./[17]-Static-Typing.md)**  
    17.1 Why Use Static Typing?  
    17.2 Typing Variables, Parameters, and Returns  
    17.3 Typed Arrays and Dictionaries  
    17.4 Type Casting with `as` and `is`  
    17.5 Working with `Variant`  
    17.6 Safe vs Unsafe Lines (Editor Hints)  
    17.7 Enabling Typing Warnings in Project Settings  

**Object-Oriented Programming**  

18. **[Classes & Scripts](./[18]-Classes-and-Scripts.md)**  
    18.1 Every Script is a Class  
    18.2 `extends` and Base Node Types  
    18.3 `class_name` and Global Classes  
    18.4 Member Variables vs Local Variables  
    18.5 The `_init()` Constructor  
    18.6 Creating Instances with `.new()`  
    18.7 Inner Classes  
    18.8 `RefCounted`, `Object`, and `Node` Memory Rules (`free()`, `queue_free()`)  
19. **[Inheritance & Polymorphism](./[19]-Inheritance-and-Polymorphism.md)**  
    19.1 What is Inheritance?  
    19.2 Extending Your Own Scripts  
    19.3 Overriding Methods  
    19.4 The `super()` Call  
    19.5 Polymorphism and Duck Typing  
    19.6 `is` Checks and `has_method()`  
    19.7 Composition vs Inheritance in Godot  
20. **[Encapsulation & Abstraction](./[20]-Encapsulation-and-Abstraction.md)**  
    20.1 What is Encapsulation?  
    20.2 The Underscore Convention for Private Members  
    20.3 Exposing Data with Methods  
    20.4 Abstraction with Virtual-Style Methods  
    20.5 Interfaces via Duck Typing and Groups  
21. **[Properties: Setters & Getters](./[21]-Properties.md)**  
    21.1 Why Use Properties?  
    21.2 The `set` and `get` Syntax  
    21.3 Setter Validation and Side Effects  
    21.4 Read-Only and Computed Properties  
    21.5 Properties with `@export`  
22. **[Static Members & Virtual Methods](./[22]-Static-and-Virtual-Methods.md)**  
    22.1 Static Variables (`static var`)  
    22.2 Static Functions (`static func`)  
    22.3 The Static Constructor (`_static_init()`)  
    22.4 Built-in Virtual Methods (`_ready`, `_process`, `_input`, etc.)  
    22.5 Writing Your Own Overridable Methods  
23. **[Custom Resources](./[23]-Custom-Resources.md)**  
    23.1 What is a `Resource`?  
    23.2 Defining a Custom Resource (`extends Resource`)  
    23.3 Exporting Resource Properties to the Inspector  
    23.4 Creating `.tres` Files  
    23.5 Sharing Data Between Nodes  
    23.6 Resources as Data Containers (Items, Stats, Settings)  
    23.7 Saving and Loading Resources  

**Godot Core Concepts**  

24. **[Nodes, Scenes & the Scene Tree](./[24]-Nodes-and-Scenes.md)**  
    24.1 Nodes as Building Blocks  
    24.2 Scenes as Reusable Node Trees  
    24.3 The Scene Tree and the Root Viewport  
    24.4 Accessing Nodes: `get_node()`, `$`, `%` (Unique Names)  
    24.5 `get_parent()`, `get_children()`, `find_child()`  
    24.6 Instancing Scenes with `preload()` / `load()` and `PackedScene.instantiate()`  
    24.7 Adding and Removing Nodes at Runtime (`add_child()`, `queue_free()`)  
    24.8 Changing Scenes (`change_scene_to_file()`)  
    24.9 Scene Organization Best Practices  
25. **[Script Lifecycle & Built-in Callbacks](./[25]-Lifecycle-and-Callbacks.md)**  
    25.1 `_init()`, `_enter_tree()`, `_ready()`, `_exit_tree()`  
    25.2 `_process(delta)`  
    25.3 `_physics_process(delta)`  
    25.4 Understanding `delta` and Frame-Rate Independence  
    25.5 `_input()`, `_unhandled_input()`, `_unhandled_key_input()`  
    25.6 Notifications (`_notification()`)  
    25.7 Execution Order of Nodes  
    25.8 Pausing and Process Modes  
26. **[Signals](./[26]-Signals.md)**  
    26.1 What Are Signals? (The Observer Pattern)  
    26.2 Built-in Signals (`pressed`, `body_entered`, `timeout`, etc.)  
    26.3 Connecting Signals in the Editor  
    26.4 Connecting Signals in Code (`connect()`)  
    26.5 Declaring and Emitting Custom Signals  
    26.6 Signal Parameters  
    26.7 Disconnecting and One-Shot Connections  
    26.8 Awaiting Signals  
    26.9 "Call Down, Signal Up" Design Rule  
27. **[Annotations](./[27]-Annotations.md)**  
    27.1 What Are Annotations (`@`)?  
    27.2 `@export` and Its Variants (`@export_range`, `@export_enum`, `@export_file`, `@export_group`, etc.)  
    27.3 `@onready`  
    27.4 `@tool`  
    27.5 `@icon` and `@static_unload`  
    27.6 `@warning_ignore`  
    27.7 `@rpc`  
28. **[Input Handling](./[28]-Input-Handling.md)**  
    28.1 The Input Map and Actions  
    28.2 Polling with the `Input` Singleton (`is_action_pressed`, `get_axis`, `get_vector`)  
    28.3 Event-Based Input with `InputEvent`  
    28.4 Keyboard, Mouse, and Touch Events  
    28.5 Gamepad and Controller Support  
    28.6 Remapping Controls at Runtime  
    28.7 Handling Input Order and Consuming Events  
29. **[Coroutines & `await`](./[29]-Coroutines-and-Await.md)**  
    29.1 What is a Coroutine?  
    29.2 `await` with Signals  
    29.3 `await` with Timers (`get_tree().create_timer()`)  
    29.4 Awaiting Other Coroutine Functions  
    29.5 Common Pitfalls (Freed Nodes, Lost Execution)  
30. **[Groups, Autoloads & Singletons](./[30]-Groups-and-Autoloads.md)**  
    30.1 Node Groups (`add_to_group()`, `get_tree().call_group()`)  
    30.2 What is an Autoload?  
    30.3 Creating and Registering Autoloads  
    30.4 Using Autoloads for Global State (Score, Settings, Events Bus)  
    30.5 The Signal/Event Bus Pattern  
    30.6 When Not to Use Autoloads  
31. **[Timers & Tweens](./[31]-Timers-and-Tweens.md)**  
    31.1 The `Timer` Node  
    31.2 One-Off Timers in Code  
    31.3 Creating Tweens (`create_tween()`)  
    31.4 Tweening Properties, Methods, and Callbacks  
    31.5 Easing and Transition Types  
    31.6 Sequencing and Parallel Tweens  

**Game Systems**  

32. **[2D Physics & Movement](./[32]-2D-Physics-and-Movement.md)**  
    32.1 The 2D Node Family (`Node2D`, `Sprite2D`, `Camera2D`)  
    32.2 `CharacterBody2D` and `move_and_slide()`  
    32.3 Top-Down Movement  
    32.4 Platformer Movement (Gravity, Jumping, Coyote Time)  
    32.5 `RigidBody2D` and `StaticBody2D`  
    32.6 `Area2D` and Overlap Detection  
    32.7 Collision Shapes, Layers, and Masks  
    32.8 Raycasting (`RayCast2D`, Physics Queries)  
    32.9 TileMaps and TileSets  
33. **[3D Basics: Movement & Cameras](./[33]-3D-Basics.md)**  
    33.1 The 3D Node Family (`Node3D`, `MeshInstance3D`, `Camera3D`)  
    33.2 `CharacterBody3D` and First/Third-Person Movement  
    33.3 Rotation, Basis, and Looking at Targets  
    33.4 `RigidBody3D` and `Area3D`  
    33.5 Lights, Materials, and Environments  
    33.6 Raycasts in 3D  
34. **[Animation](./[34]-Animation.md)**  
    34.1 `AnimationPlayer` Basics  
    34.2 `AnimatedSprite2D` and Sprite Sheets  
    34.3 `AnimationTree` and State Machines  
    34.4 Controlling Animations from Code  
    34.5 Animation Signals and Method Tracks  
    34.6 Writing a Simple State Machine in GDScript  
35. **[User Interfaces with Control Nodes](./[35]-User-Interfaces.md)**  
    35.1 The `Control` Node and Anchors  
    35.2 Containers (`VBoxContainer`, `HBoxContainer`, `GridContainer`, `MarginContainer`)  
    35.3 Common Widgets (`Label`, `Button`, `LineEdit`, `TextureRect`, `ProgressBar`)  
    35.4 Connecting UI Signals  
    35.5 Themes and Styling  
    35.6 Building a Main Menu, Pause Menu, and HUD  
    35.7 Focus, Keyboard, and Controller Navigation  
36. **[Audio](./[36]-Audio.md)**  
    36.1 `AudioStreamPlayer`, `AudioStreamPlayer2D`, `AudioStreamPlayer3D`  
    36.2 Playing Sound Effects and Music from Code  
    36.3 Audio Buses and Effects  
    36.4 Volume Control and Mixing Settings  
37. **[Saving & Loading Data](./[37]-Saving-and-Loading.md)**  
    37.1 `FileAccess` for Reading and Writing Files  
    37.2 Saving to `user://`  
    37.3 JSON with `JSON.stringify()` and `JSON.parse_string()`  
    37.4 `ConfigFile` for Settings  
    37.5 `var_to_str`, `store_var`, and Binary Saves  
    37.6 Saving with Custom Resources (`ResourceSaver`)  
    37.7 Designing a Save System  
38. **[Randomness, Math & Interpolation](./[38]-Randomness-and-Math.md)**  
    38.1 `randi()`, `randf()`, `randi_range()`, `randomize()`  
    38.2 `RandomNumberGenerator`  
    38.3 `lerp()`, `inverse_lerp()`, `remap()`, `smoothstep()`  
    38.4 `clamp()`, `wrap()`, `snapped()`, `abs()`, `sign()`  
    38.5 Trigonometry (`sin`, `cos`, `atan2`, `deg_to_rad`)  
    38.6 Noise (`FastNoiseLite`) and Procedural Generation  
    38.7 Pathfinding with `AStar2D` and `NavigationAgent`  

**Advanced Language Features**  

39. **[Lambdas & Callables](./[39]-Lambdas-and-Callables.md)**  
    39.1 What is a `Callable`?  
    39.2 Lambda Functions  
    39.3 Capturing Variables  
    39.4 `bind()`, `unbind()`, and `call()`  
    39.5 Using Callables with `map`, `filter`, `sort_custom`, and Signals  
40. **[Tool Scripts & Editor Plugins](./[40]-Tool-Scripts-and-Plugins.md)**  
    40.1 What is `@tool`?  
    40.2 Running Code in the Editor  
    40.3 `EditorScript` One-Off Tools  
    40.4 Custom Nodes and Inspector Plugins  
    40.5 Creating and Distributing Add-ons  
41. **[Threading & Performance](./[41]-Threading-and-Performance.md)**  
    41.1 The Main Thread and Why It Matters  
    41.2 `Thread`, `Mutex`, and `Semaphore`  
    41.3 `WorkerThreadPool`  
    41.4 `call_deferred()` and Thread Safety  
    41.5 Performance Tips (Typed Code, Object Pooling, Avoiding `get_node()` in `_process`)  
42. **[Networking & Multiplayer](./[42]-Networking-and-Multiplayer.md)**  
    42.1 High-Level Multiplayer API Overview  
    42.2 `ENetMultiplayerPeer` and `WebSocketMultiplayerPeer`  
    42.3 Servers, Clients, and Peer IDs  
    42.4 Remote Procedure Calls (`@rpc`)  
    42.5 `MultiplayerSpawner` and `MultiplayerSynchronizer`  
    42.6 `HTTPRequest` for Web APIs  
43. **[GDExtension & C# Interop](./[43]-GDExtension-and-CSharp.md)**  
    43.1 GDScript vs C# vs C++: Choosing a Language  
    43.2 Mixing GDScript and C# in One Project  
    43.3 What is GDExtension?  
    43.4 Calling Native Code from GDScript  

**Tooling & Best Practices**  

44. **[Debugging & Profiling](./[44]-Debugging-and-Profiling.md)**  
    44.1 Using `print()`, `print_debug()`, and `print_rich()`  
    44.2 Breakpoints and Stepping Through Code  
    44.3 The Debugger Panel (Stack, Variables, Errors)  
    44.4 The Remote Scene Tree  
    44.5 Visible Collision Shapes and Navigation Debugging  
    44.6 The Profiler, Monitors, and Video RAM Tools  
45. **[GDScript Style Guide & Best Practices](./[45]-Best-Practices-and-Style.md)**  
    45.1 Official Style Guide Overview  
    45.2 Naming Conventions  
    45.3 Code Order Inside a Script  
    45.4 Formatting, Line Length, and Comments  
    45.5 Project and Scene Organization  
    45.6 Linting and Formatting with `gdtoolkit` (`gdlint`, `gdformat`)  
46. **[Exporting & Deploying Your Game](./[46]-Exporting-and-Deployment.md)**  
    46.1 Export Templates  
    46.2 Export Presets (Windows, macOS, Linux)  
    46.3 Exporting for Web (HTML5)  
    46.4 Exporting for Android and iOS  
    46.5 Resource Packs and Updates  
    46.6 Publishing on itch.io and Steam  