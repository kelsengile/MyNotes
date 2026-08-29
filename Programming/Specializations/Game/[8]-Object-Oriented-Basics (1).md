[Previous](./%5B7%5D-Functions-and-Scope%20%281%29.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./%5B9%5D-Collections-and-Data-Structures%20%281%29.md)

*Core Programming Concepts*

# Lesson 8 - Object-Oriented Basics for Games (Classes, Components)

## 8.1 Classes and Objects

A **class** is a blueprint describing a type of thing — its data (fields) and behavior (methods). An **object** (or instance) is a concrete thing built from that blueprint.

```csharp
class Enemy {
    public int health = 50;
    public void TakeDamage(int amount) {
        health -= amount;
    }
}

Enemy goblin = new Enemy();
Enemy dragon = new Enemy();
```

`goblin` and `dragon` are two separate objects, each with their own `health`, even though they share the same `Enemy` class definition.

---

## 8.2 Inheritance

Inheritance lets one class reuse and extend another's behavior:

```csharp
class Character {
    public int health;
    public virtual void Die() { /* default death behavior */ }
}

class Boss : Character {
    public override void Die() { /* boss-specific death: drop loot, play cutscene */ }
}
```

`Boss` automatically has everything `Character` has, plus its own overrides. Inheritance is powerful but can become rigid — deep inheritance chains (`Character → Humanoid → Warrior → Paladin → CrusaderPaladin`) get hard to maintain, which is why many engines favor components instead.

---

## 8.3 Components and Composition

Rather than one giant class trying to do everything, **composition** builds behavior by attaching small, focused components to an object:

- A `Player` GameObject might have a `MovementComponent`, a `HealthComponent`, and an `InventoryComponent` attached, rather than one massive `Player` class containing all that logic.
- Each component can be reused on different objects — an `EnemyAI` could reuse the same `HealthComponent` as the player.

---

## 8.4 Composition over Inheritance in Game Engines

Modern engines (Unity's `MonoBehaviour` components, Godot's Nodes, Unreal's Actor Components) are built around composition specifically because game objects often need to mix and match behaviors in ways a rigid inheritance tree can't express cleanly. A general rule of thumb: use inheritance for a true "is-a" relationship (a `Boss` *is a* `Character`), and use composition for "has-a" relationships (a `Player` *has a* `HealthComponent`). This is explored further in Lesson 12.

[Previous](./%5B7%5D-Functions-and-Scope%20%281%29.md) | [Table of Contents](./[0]-Introduction-to-Game-Development.md) | [Next](./%5B9%5D-Collections-and-Data-Structures%20%281%29.md)
