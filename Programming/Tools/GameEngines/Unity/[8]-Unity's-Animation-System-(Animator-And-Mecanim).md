[Previous](./[7]-The-Unity-UI-System-(Canvas-And-RectTransform).md) | [Table of Contents](./[0]-Introduction-to-Unity.md) | [Next](./[9]-Building-And-Exporting-A-Unity-Project.md)

*Building Features*

# Lesson 8 - Unity's Animation System (Animator And Mecanim)

## 8.1 Animation Clips

An **Animation Clip** is a single recorded piece of movement or change over time — a walk cycle, a jump, a door opening — stored as an asset (`.anim`) containing keyframed values for one or more properties.

Clips can come from two places:

- **Imported** alongside a 3D model (common for character animations exported from Blender/Maya as part of an `.fbx` file) — Unity automatically extracts these into individual Clip assets.
- **Created directly in Unity** using the **Animation window** (`Window > Animation > Animation`), which lets you keyframe almost any property — position, color, even script fields — directly inside the editor, similar to Godot's `AnimationPlayer` timeline.

```
Assets/Animations/
├── Player_Idle.anim
├── Player_Walk.anim
├── Player_Jump.anim
└── Player_Attack.anim
```

A Clip alone doesn't play automatically — it needs to be placed into an **Animator Controller** (next) that decides *when* each Clip should play.

---

## 8.2 The Animator Controller And State Machines

An **Animator Controller** is a state machine asset that organizes multiple Animation Clips into named **states**, with **transitions** between them controlled by conditions — Unity's overall animation system is historically called **Mecanim**, and the Animator Controller is its central piece.

```
        [Idle] ──speed > 0.1──▶ [Walk]
          ▲                        │
          └────speed < 0.1─────────┘
                    │
              isJumping = true
                    ▼
                 [Jump]
```

Each state references one Animation Clip to play while active. Transitions are driven by **Parameters** — typed variables on the Animator (`float`, `int`, `bool`, `Trigger`) that your script sets, and that transitions check as conditions:

```csharp
using UnityEngine;

public class PlayerAnimation : MonoBehaviour
{
    private Animator animator;
    private Rigidbody2D rb;

    void Awake()
    {
        animator = GetComponent<Animator>();
        rb = GetComponent<Rigidbody2D>();
    }

    void Update()
    {
        float speed = Mathf.Abs(rb.linearVelocity.x);
        animator.SetFloat("Speed", speed);

        if (Input.GetButtonDown("Jump"))
        {
            animator.SetTrigger("Jump");
        }
    }
}
```

The script never plays a Clip directly — it only sets parameters (`"Speed"`, `"Jump"`), and the Animator Controller's transitions decide which state (and therefore which Clip) is actually active. This separation keeps animation logic (which state follows which) out of gameplay scripts, and is the same conceptual split as Godot's `AnimationTree` driven by exported parameters.

---

## 8.3 Blend Trees

A **Blend Tree** is a special kind of state inside an Animator Controller that smoothly blends between *multiple* Clips based on a parameter, rather than switching sharply between discrete states — ideal for movement that should transition gradually, like walking speeding up into running.

```
Blend Tree (parameter: Speed)
   0.0 ──────── 0.5 ──────── 1.0
   Idle          Walk          Run
   (blends smoothly between neighboring clips as Speed changes)
```

Rather than jarring jumps between an `Idle` and `Walk` state, a 1D Blend Tree driven by a `Speed` float smoothly cross-fades between the `Idle`, `Walk`, and `Run` clips as the value moves between them — the character's animation speeds up fluidly rather than snapping. 2D Blend Trees extend this to two parameters at once (e.g. blending between directional movement clips based on both `X` and `Y` input), commonly used for top-down or strafe-based movement.

Blend Trees are set up visually in the Animator window — you add the Clips you want blended, assign the driving parameter(s), and Unity handles the interpolation math.

---

## 8.4 Animation Events

**Animation Events** let a Clip call a method on a script at a specific point during playback — the standard way to synchronize gameplay logic with a precise animation frame, such as spawning a hit effect exactly when a sword swing's blade reaches its target, or playing a footstep sound on the exact frame a foot touches the ground.

Set up in the Animation window: select a frame on the Clip's timeline, right-click, and choose **Add Animation Event**, then specify which method to call.

```csharp
// Called automatically by an Animation Event during the Attack clip
public void OnSwordHitFrame()
{
    DealDamageToTarget();
    Instantiate(hitEffectPrefab, swordTip.position, Quaternion.identity);
}
```

This is meaningfully more precise than trying to time gameplay effects with a `Coroutine` and a guessed delay (Lesson 5.4) — the event fires exactly on the animation frame you chose, staying in sync even if the Clip's overall length or playback speed changes later. Animation Events are the standard tool anywhere gameplay logic needs to line up exactly with a specific visual moment.

[Previous](./[7]-The-Unity-UI-System-(Canvas-And-RectTransform).md) | [Table of Contents](./[0]-Introduction-to-Unity.md) | [Next](./[9]-Building-And-Exporting-A-Unity-Project.md)
