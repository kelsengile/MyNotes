[Previous](./[3]-GameObjects,-Components,-And-Prefabs-In-Unity.md) | [Table of Contents](./[0]-Introduction-to-Unity.md) | [Next](./[5]-MonoBehaviour-Lifecycle-Methods.md)

*Core Concepts And Scripting*

# Lesson 4 - Introduction To C# Scripting In Unity

## 4.1 Creating A MonoBehaviour Script

Unity scripts are C# classes that extend `MonoBehaviour`, the base class that lets a script be attached to a GameObject as a Component. Creating one is done from the Project window: **Right-click > Create > C# Script**, which generates a `.cs` file whose name must match its class name exactly.

```csharp
using UnityEngine;

public class PlayerController : MonoBehaviour
{
    void Start()
    {
        Debug.Log("Player has entered the scene!");
    }
}
```

- `using UnityEngine;` imports Unity's core API — `MonoBehaviour`, `Debug`, `Transform`, and everything else Unity-specific.
- `public class PlayerController : MonoBehaviour` declares this script as a Component, inheriting from `MonoBehaviour`.
- `void Start()` is a **lifecycle method** (covered fully in Lesson 5) that Unity calls automatically once the object is active in the scene.

Double-clicking the script in the Project window opens it in your configured code editor (commonly Visual Studio or Visual Studio Code, chosen during/after Hub installation).

---

## 4.2 Public Fields And The Inspector

Any `public` field on a `MonoBehaviour` automatically appears in the Inspector when the script is attached to a GameObject — Unity's version of GDScript's `@export`, but the default rather than something you opt into:

```csharp
using UnityEngine;

public class PlayerController : MonoBehaviour
{
    public float speed = 5.0f;
    public float jumpForce = 8.0f;
    public int maxHealth = 100;
}
```

With this attached, `speed`, `jumpForce`, and `maxHealth` all become editable fields in the Inspector, letting you tune gameplay values without touching code — exactly the same benefit `@export` provides in GDScript.

For fields you want editable in the Inspector but not accessible from *other* scripts, `[SerializeField]` on a private field gives you both:

```csharp
[SerializeField] private float speed = 5.0f;
```

This is generally considered better practice than a fully public field once a project grows, since it keeps the field's visibility to other code intentionally restricted while still exposing it for designers to tune.

---

## 4.3 Attaching Scripts To GameObjects

A script only runs once it's attached as a Component to a GameObject — writing the `.cs` file alone does nothing by itself. Attach it the same way as any other Component:

- Drag the script file from the Project window directly onto a GameObject in the Hierarchy (or onto the Inspector), **or**
- Select the GameObject, click **Add Component** in the Inspector, and search for the script's class name.

Once attached, the script's public fields appear in the Inspector immediately, and its lifecycle methods (`Start`, `Update`, etc.) begin running as soon as you enter Play Mode.

A single GameObject can have multiple scripts attached at once — it's common and often good practice to split behavior into several small, focused scripts (e.g. `PlayerMovement`, `PlayerHealth`, `PlayerInventory`) rather than one large script handling everything, mirroring the same "many small Components" philosophy from Lesson 3.1.

---

## 4.4 Accessing Other Components (GetComponent)

Since behavior is split across multiple Components, scripts frequently need to reach other Components on the same (or a different) GameObject. `GetComponent<T>()` is the standard way to do this:

```csharp
using UnityEngine;

public class PlayerController : MonoBehaviour
{
    private Rigidbody2D rb;
    private SpriteRenderer sr;

    void Awake()
    {
        rb = GetComponent<Rigidbody2D>();
        sr = GetComponent<SpriteRenderer>();
    }

    void Update()
    {
        if (Input.GetKeyDown(KeyCode.Space))
        {
            sr.color = Color.red;
        }
    }
}
```

Caching the result in `Awake()` (Lesson 5.1) rather than calling `GetComponent<T>()` repeatedly inside `Update()` is standard practice — `GetComponent` involves a lookup cost, and calling it every single frame is unnecessary work when the reference never changes.

Related methods reach further than just the current GameObject:

```csharp
GetComponentInChildren<Animator>();   // searches this GameObject and its children
GetComponentInParent<HealthBar>();    // searches this GameObject and its parents
GameObject.Find("Player");            // finds a GameObject anywhere in the scene by name (use sparingly — slow)
```

`GameObject.Find()` and similar scene-wide searches work but are relatively slow and fragile (they break if you rename something) — for that reason, most real projects prefer assigning references directly in the Inspector (a public/`[SerializeField]` field you drag a GameObject onto) wherever possible, falling back to runtime lookups only when the reference genuinely can't be known ahead of time.

[Previous](./[3]-GameObjects,-Components,-And-Prefabs-In-Unity.md) | [Table of Contents](./[0]-Introduction-to-Unity.md) | [Next](./[5]-MonoBehaviour-Lifecycle-Methods.md)
