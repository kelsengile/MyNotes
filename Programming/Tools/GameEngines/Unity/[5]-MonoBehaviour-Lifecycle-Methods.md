[Previous](./[4]-Introduction-To-C%23-Scripting-In-Unity.md) | [Table of Contents](./[0]-Introduction-to-Unity.md) | [Next](./[6]-Unity-Physics-And-Colliders.md)

*Core Concepts And Scripting*

# Lesson 5 - MonoBehaviour Lifecycle Methods

## 5.1 Awake vs Start

Unity calls several methods on a `MonoBehaviour` automatically at specific points, and the two you'll reach for first — `Awake()` and `Start()` — look similar but serve different purposes:

- **`Awake()`** — called as soon as the GameObject is instantiated/loaded, before any `Start()` methods run, and even if the script/GameObject is disabled. Best for internal setup that doesn't depend on other objects — caching your own Components via `GetComponent`, initializing internal variables.
- **`Start()`** — called just before the first frame's `Update()`, but only if the GameObject is active/enabled. Best for setup that *does* depend on other objects already being initialized, since by the time any `Start()` runs, every object's `Awake()` has already completed.

```csharp
void Awake()
{
    rb = GetComponent<Rigidbody2D>();   // safe: only touches this object
}

void Start()
{
    GameManager.Instance.RegisterPlayer(this);   // safe: GameManager's Awake already ran
}
```

The rule of thumb: **use `Awake()` for "getting my own house in order," and `Start()` for "now that everyone's house is in order, coordinate with others."** Getting this backwards is a classic source of `NullReferenceException` bugs when one object's `Start()` runs before another object it depends on has finished its own `Awake()`.

---

## 5.2 Update, FixedUpdate, And LateUpdate

Three more lifecycle methods run repeatedly, but at different rates and for different purposes:

| Method | Runs | Use For |
|---|---|---|
| `Update()` | Once per rendered frame (variable rate) | Input handling, non-physics movement, general per-frame logic |
| `FixedUpdate()` | At a fixed rate, independent of framerate (default 50Hz) | Physics — applying forces, moving a `Rigidbody` |
| `LateUpdate()` | Once per frame, after all `Update()` calls have finished | Camera following, anything that needs to happen *after* everything else has moved |

```csharp
public float speed = 5f;
private Rigidbody2D rb;

void Update()
{
    // Read input every frame for responsiveness
    horizontalInput = Input.GetAxis("Horizontal");
}

void FixedUpdate()
{
    // Apply movement in the physics step
    rb.linearVelocity = new Vector2(horizontalInput * speed, rb.linearVelocity.y);
}

void LateUpdate()
{
    // Camera follows the player, after the player has already moved this frame
    transform.position = player.position + cameraOffset;
}
```

This split mirrors Godot's `_process` vs `_physics_process` distinction from the Godot Topic almost exactly: **anything touching Rigidbody physics belongs in `FixedUpdate()`, anything purely visual/input-related belongs in `Update()`**, and `LateUpdate()` exists specifically to guarantee correct ordering for things like cameras that must react to where everything else already ended up this frame.

---

## 5.3 OnEnable/OnDisable And OnDestroy

A few lifecycle methods fire around a GameObject's activation state and eventual removal:

- **`OnEnable()`** — called whenever the GameObject (or the script itself) becomes active/enabled, including every time after the first (unlike `Awake`/`Start`, which only run once).
- **`OnDisable()`** — called whenever it becomes inactive/disabled, or right before the object is destroyed.
- **`OnDestroy()`** — called once, when the GameObject is actually destroyed (via `Destroy()` or a scene unloading).

```csharp
void OnEnable()
{
    GameEvents.OnPlayerDied += HandlePlayerDied;   // subscribe
}

void OnDisable()
{
    GameEvents.OnPlayerDied -= HandlePlayerDied;   // unsubscribe
}
```

This enable/disable pairing is the standard place to subscribe and unsubscribe from events (C#'s equivalent of connecting/disconnecting signals) — subscribing in `OnEnable` and unsubscribing in `OnDisable` prevents a common bug class where a disabled or destroyed object's method still gets called because it was never properly unsubscribed, which can throw errors or silently leak memory over time.

---

## 5.4 Coroutines

Sometimes you need code to pause and resume over multiple frames — waiting a few seconds, fading something out gradually, or running a sequence of timed steps — without blocking the rest of the game. Unity handles this with **Coroutines**: methods that use `yield return` to pause and resume.

```csharp
using System.Collections;
using UnityEngine;

public class Flasher : MonoBehaviour
{
    private SpriteRenderer sr;

    void Awake()
    {
        sr = GetComponent<SpriteRenderer>();
    }

    public void FlashRed()
    {
        StartCoroutine(FlashRoutine());
    }

    private IEnumerator FlashRoutine()
    {
        sr.color = Color.red;
        yield return new WaitForSeconds(0.2f);
        sr.color = Color.white;
    }
}
```

- A Coroutine is declared as a method returning `IEnumerator`.
- `StartCoroutine()` begins running it.
- `yield return new WaitForSeconds(x)` pauses execution for `x` seconds before continuing, without freezing the rest of the game in the meantime.

Other common yield instructions include `yield return null` (wait one frame) and `yield return new WaitUntil(() => condition)` (wait until a condition becomes true). Coroutines are ideal for short, self-contained timed sequences; for more complex asynchronous logic (networking, loading), Unity also supports C#'s native `async`/`await`, though Coroutines remain the more common tool for everyday gameplay timing.

[Previous](./[4]-Introduction-To-C%23-Scripting-In-Unity.md) | [Table of Contents](./[0]-Introduction-to-Unity.md) | [Next](./[6]-Unity-Physics-And-Colliders.md)
