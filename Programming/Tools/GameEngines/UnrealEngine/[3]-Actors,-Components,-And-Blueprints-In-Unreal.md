[Previous](./[2]-The-Unreal-Editor-Interface.md) | [Table of Contents](./[0]-Introduction-to-UnrealEngine.md) | [Next](./[4]-Introduction-To-Blueprint-Visual-Scripting.md)

*Core Concepts And Scripting*

# Lesson 3 - Actors, Components, And Blueprints In Unreal

## 3.1 Actors As Unreal's Building Block

An **Actor** is Unreal's base class for anything that can be placed into a level — a light, a mesh, a player character, a trigger volume. This plays a similar role to a GameObject in Unity or a Node in Godot, and like Unity's GameObjects, an Actor's actual behavior and appearance come from **Components** attached to it rather than from the Actor class alone doing everything.

```
BP_Enemy (Actor)
├── StaticMeshComponent   (visual mesh)
├── CapsuleComponent       (collision)
├── CameraComponent         (if this Actor needs a camera)
└── (Blueprint Graph / C++ logic)
```

Unreal ships several specialized Actor subclasses worth knowing, since you'll encounter them constantly:

| Subclass | Purpose |
|---|---|
| `Pawn` | An Actor that can be possessed/controlled (by a player or AI) |
| `Character` | A `Pawn` specialized for humanoid movement, with built-in walking/jumping/falling logic |
| `StaticMeshActor` | A simple Actor wrapping a single static mesh — the most common way to place level geometry |
| `PlayerController` | Represents a player's control/input, separate from the Pawn they're possessing |
| `GameMode` | Defines the rules of the current game session (which Pawn class to spawn, scoring, win conditions) |

---

## 3.2 Components (Static Mesh, Collision, Camera)

Components attach to an Actor to give it capabilities, added through the **Blueprint Editor's Components panel** or the **Details Panel** when configuring a placed Actor:

- **StaticMeshComponent** — displays a static (non-animated) 3D mesh; the most common visual component for props and level geometry.
- **SkeletalMeshComponent** — displays an animated, bone-driven mesh (characters, creatures) — paired with the Animation Blueprint system from Lesson 8.
- **CapsuleComponent / BoxComponent / SphereComponent** — collision shapes, defining what physically interacts with the Actor.
- **CameraComponent** — defines a viewpoint; attached to a Pawn to give the player a camera that follows them.
- **SpringArmComponent** — commonly paired with a Camera, providing smooth camera-follow behavior with automatic collision handling (preventing the camera from clipping through walls) — a very common pattern for third-person cameras.

```
BP_ThirdPersonCharacter (Character)
├── CapsuleComponent          (collision)
├── SkeletalMeshComponent       (the character model)
├── SpringArmComponent           (camera boom, follows and avoids collision)
└── CameraComponent               (attached to end of SpringArm)
```

Components themselves can be nested — attaching a Component as a child of another Component within the same Actor — which is how the `SpringArm` → `Camera` relationship above works: the Camera is attached to (and inherits the transform of) the end of the Spring Arm.

---

## 3.3 Blueprint Classes vs C++ Classes

Unreal is unusual among the engines in this course series in offering **two first-class ways to define an Actor's class**, and it's common — even encouraged — to mix both in the same project:

| | Blueprint Class | C++ Class |
|---|---|---|
| **Authoring** | Visual node graph (Lesson 4) | Text-based C++ code (Lesson 5) |
| **Iteration speed** | Very fast — compile and test changes almost instantly | Slower — requires a full recompile for some changes |
| **Best for** | Gameplay logic, designer/artist-friendly tweaking, rapid prototyping | Performance-critical systems, complex algorithms, low-level engine access |
| **Who typically writes it** | Designers, artists, and programmers alike | Programmers |

A very common production pattern — worth knowing even as a beginner — is to write a **C++ base class** that handles performance-sensitive core logic and exposes clearly-defined hooks, then create a **Blueprint subclass** of that C++ class to handle the more iterative, designer-facing behavior on top of it. This gets the performance/structure benefits of C++ and the iteration speed of Blueprints in the same Actor.

---

## 3.4 The Actor Lifecycle (BeginPlay, Tick)

Actors have lifecycle events analogous to Godot's `_ready`/`_process` or Unity's `Start`/`Update`, available in both Blueprints and C++:

- **BeginPlay** — called once, when the Actor is spawned into a running game. Used for initial setup — the equivalent of Unity's `Start()` or Godot's `_ready()`.
- **Tick** — called every frame, with a `DeltaTime` value (equivalent to Unity's `Update(deltaTime)` or Godot's `_process(delta)`), used for continuous per-frame behavior.

In Blueprints, these appear as **Event nodes** at the start of an Event Graph:

```
[Event BeginPlay] ──▶ [Print String: "Actor has spawned!"]

[Event Tick] ──▶ [Add Movement Input] ──▶ ...
      (DeltaTime pin available for frame-rate-independent math)
```

In C++, the equivalent looks like:

```cpp
void AMyActor::BeginPlay()
{
    Super::BeginPlay();
    UE_LOG(LogTemp, Log, TEXT("Actor has spawned!"));
}

void AMyActor::Tick(float DeltaTime)
{
    Super::Tick(DeltaTime);
    // per-frame logic here
}
```

Note the `Super::BeginPlay()` / `Super::Tick(DeltaTime)` calls — always call the parent implementation first in C++ overrides of these functions, since skipping it can silently break built-in Actor behavior that depends on it running. Like the frame-rate-independence lessons from the Godot and Unity Topics, any movement calculated in `Tick` should be multiplied by `DeltaTime` to stay consistent across different framerates. Tick can also be disabled per-Actor (`Actor Tick > Start with Tick Enabled`) when an Actor doesn't need per-frame logic, which is worth doing for performance on Actors that only ever react to events.

[Previous](./[2]-The-Unreal-Editor-Interface.md) | [Table of Contents](./[0]-Introduction-to-UnrealEngine.md) | [Next](./[4]-Introduction-To-Blueprint-Visual-Scripting.md)