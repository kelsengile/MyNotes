[Previous](./[4]-Introduction-To-Blueprint-Visual-Scripting.md) | [Table of Contents](./[0]-Introduction-to-UnrealEngine.md) | [Next](./[6]-Unreal-Physics-And-Collision.md)

*Core Concepts And Scripting*

# Lesson 5 - Introduction To C++ In Unreal Engine

## 5.1 Why Use C++ Alongside Blueprints

Blueprints (Lesson 4) are excellent for iteration speed and accessibility, but they're not always the right tool for everything:

- **Performance** — Blueprint graphs are generally slower than compiled C++ for computationally heavy logic (complex math running every frame across many Actors, for example).
- **Low-level access** — some Engine systems and third-party plugins are only accessible from C++.
- **Structure at scale** — very large, complex graphs can become harder to read and maintain than well-organized C++ code, especially across a team.

The realistic answer for most projects isn't "C++ instead of Blueprints" but "C++ **and** Blueprints, each where they fit" — exactly the base-class-plus-subclass pattern introduced in Lesson 3.3. C++ defines the performance-sensitive skeleton; Blueprints extend it with fast-iterating, designer-facing behavior on top.

---

## 5.2 UCLASS, UPROPERTY, And UFUNCTION Basics

Unreal's C++ isn't quite standard C++ — it's extended by **UnrealHeaderTool (UHT)**, which reads special macros in your code to hook classes, properties, and functions into Unreal's reflection system (what makes them visible to Blueprints, the Editor, and serialization).

```cpp
#pragma once

#include "CoreMinimal.h"
#include "GameFramework/Actor.h"
#include "MyEnemy.generated.h"

UCLASS()
class MYGAME_API AMyEnemy : public AActor
{
    GENERATED_BODY()

public:
    AMyEnemy();

    UPROPERTY(EditAnywhere, BlueprintReadWrite, Category = "Stats")
    float Health = 100.0f;

    UFUNCTION(BlueprintCallable, Category = "Combat")
    void TakeDamage(float Amount);

protected:
    virtual void BeginPlay() override;

public:
    virtual void Tick(float DeltaTime) override;
};
```

- **`UCLASS()`** — marks this class as a Unreal class, making it recognized by the reflection system (required for Blueprints to be able to subclass it, among other things).
- **`UPROPERTY(...)`** — exposes a member variable. `EditAnywhere` makes it editable in the Details panel; `BlueprintReadWrite` lets Blueprint graphs both read and write it.
- **`UFUNCTION(...)`** — exposes a method. `BlueprintCallable` lets it be called from a Blueprint graph as a node, exactly like the built-in Engine nodes used in Lesson 4.
- **`GENERATED_BODY()`** — boilerplate required in every `UCLASS`, generated automatically by UHT during compilation.

---

## 5.3 Creating A C++ Actor Class

New C++ classes are created from within the Editor itself — **Tools > New C++ Class** — which generates both a header (`.h`) and source (`.cpp`) file, adds them to your project, and (after a short compile) makes the class immediately available to subclass in Blueprints, exactly like any built-in Engine class.

```cpp
// MyEnemy.cpp
#include "MyEnemy.h"

AMyEnemy::AMyEnemy()
{
    PrimaryActorTick.bCanEverTick = true;
}

void AMyEnemy::BeginPlay()
{
    Super::BeginPlay();
    UE_LOG(LogTemp, Log, TEXT("Enemy spawned with %f health"), Health);
}

void AMyEnemy::Tick(float DeltaTime)
{
    Super::Tick(DeltaTime);
}

void AMyEnemy::TakeDamage(float Amount)
{
    Health -= Amount;
    if (Health <= 0.0f)
    {
        Destroy();
    }
}
```

Unreal projects using C++ require a full **compile** step (via the Editor's built-in **Live Coding** for quick iteration, or a full rebuild from your IDE — commonly Visual Studio or JetBrains Rider) after changing code, unlike Blueprints, which apply changes as soon as you compile the graph inside the Blueprint Editor — a near-instant operation. This compile-step difference is the main reason Blueprints feel faster to iterate with day-to-day, even though C++ ultimately runs faster once compiled.

---

## 5.4 Blueprint/C++ Interoperability

Once a C++ class like `AMyEnemy` exists, you create a **Blueprint subclass** of it exactly like subclassing any built-in Engine Actor — right-click the C++ class in the Content Browser and choose **Create Blueprint class based on this**.

```
AActor (Engine C++ base class)
   └── AMyEnemy (your C++ class — Health, TakeDamage(), BeginPlay, Tick)
         └── BP_Enemy_Goblin (Blueprint subclass)
               ├── overrides: Health = 50 (via UPROPERTY EditAnywhere)
               ├── adds: on-death particle effect (pure Blueprint logic)
               └── calls: TakeDamage() from its own Blueprint graph
```

The Blueprint subclass automatically inherits everything from the C++ parent — including calling `TakeDamage()` as a normal Blueprint node, since it was marked `BlueprintCallable` — while adding Blueprint-only logic (particle effects, sound cues, designer-tuned values) on top, without touching the underlying C++ at all.

This pattern — **C++ base class, Blueprint subclasses for variation** — is genuinely one of Unreal's defining strengths as an engine: it lets programmers build a solid, performant, well-tested foundation while designers and artists iterate rapidly on top of it in Blueprints, without either group blocking the other or needing to touch the other's tools directly.

[Previous](./[4]-Introduction-To-Blueprint-Visual-Scripting.md) | [Table of Contents](./[0]-Introduction-to-UnrealEngine.md) | [Next](./[6]-Unreal-Physics-And-Collision.md)