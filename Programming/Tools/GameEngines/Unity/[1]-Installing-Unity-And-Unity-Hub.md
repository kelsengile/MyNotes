[Previous](./[0]-Introduction-to-Unity.md) | [Table of Contents](./[0]-Introduction-to-Unity.md) | [Next](./[2]-The-Unity-Editor-Interface.md)

*Getting Started*

# Lesson 1 - Installing Unity And Unity Hub

## 1.1 Unity Hub And Managing Editor Versions

Unlike Godot's single portable executable, Unity is installed through **Unity Hub** — a small launcher application you install first, which then manages the actual Unity Editor installations, your projects, and your Unity account/license.

```
┌───────────────────────────────────────┐
│  Unity Hub                              │
├───────────────────────────────────────┤
│  Projects | Installs | Learn            │
├───────────────────────────────────────┤
│  Installs:                               │
│   ▸ 2022.3.45f1 LTS                      │
│   ▸ 6000.0.32f1                          │
│  [Install Editor]                        │
└───────────────────────────────────────┘
```

This matters because, unlike many other tools, **you can have multiple Unity Editor versions installed side by side**, and each project is locked to whichever version created it. Hub is what lets you install, switch between, and manage these versions cleanly, rather than manually juggling separate downloads.

Download Unity Hub first from [docs.unity.com/en-us/hub/install-hub](https://docs.unity.com/en-us/hub/install-hub), sign in with a (free) Unity account, and then use Hub's **Installs** tab to add your first Editor version.

---

## 1.2 LTS vs Tech Stream Releases

When installing an Editor version, Unity offers two release tracks:

- **LTS (Long-Term Support)** — the recommended default for almost everyone. LTS versions receive bug fixes and stability patches for an extended period (roughly two years) without introducing new features that could break your project mid-development.
- **Tech Stream** — newer releases with the latest features, but shorter support windows and a higher chance of encountering bugs in newly-added systems.

For any project you intend to actually finish and ship, **start on the latest LTS version** — new features in Tech Stream releases are rarely worth the added instability for a learning project or most production work. You can always start a new project on a newer version later once a feature you specifically need reaches LTS.

---

## 1.3 Creating A New Project And Templates

From Hub's **Projects** tab, **New Project** prompts you to choose both an installed Editor version and a **template**:

| Template | Starts You With |
|---|---|
| **3D (Core)** / **3D (URP)** | An empty 3D scene, with Unity's standard or Universal Render Pipeline pre-configured |
| **2D (Core)** / **2D (URP)** | An empty 2D scene, camera set to orthographic by default |
| **VR** | XR-specific packages and a starter rig pre-installed |
| **Mobile** | Settings pre-tuned for mobile performance targets |

The **URP (Universal Render Pipeline)** variants are the recommended default for new projects in modern Unity — URP offers better performance across platforms (including mobile) compared to the legacy Built-In Render Pipeline, and is where the majority of new Unity features and documentation are focused.

Each new project lives in its own folder on disk, containing an `Assets/` directory (everything you create or import), a `ProjectSettings/` directory, and a `Packages/` directory (managed dependencies) — similar in spirit to Godot's self-contained project folders.

---

## 1.4 Unity License Tiers

Unity's licensing is based on your studio's or individual's prior 12-month revenue/funding, not on which features you can access — nearly all Editor functionality is available at every tier:

| Tier | Who It's For |
|---|---|
| **Personal** | Free; individuals and small teams under a certain revenue threshold |
| **Pro** | Paid subscription; teams/companies above that threshold, adds cloud/collaboration features |
| **Enterprise** | Paid, larger organizations; adds enterprise support and advanced tooling |

For learning and most hobby/indie projects, the **Personal** tier is sufficient and free — you won't hit licensing limitations while working through this Topic. Unity's official site has the current revenue thresholds if a project you're working on starts generating real income, since these figures are updated periodically and are worth checking directly rather than assuming.

[Previous](./[0]-Introduction-to-Unity.md) | [Table of Contents](./[0]-Introduction-to-Unity.md) | [Next](./[2]-The-Unity-Editor-Interface.md)
