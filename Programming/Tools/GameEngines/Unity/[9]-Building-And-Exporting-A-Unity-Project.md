[Previous](./[8]-Unity's-Animation-System-(Animator-And-Mecanim).md) | [Table of Contents](./[0]-Introduction-to-Unity.md)

*Shipping*

# Lesson 9 - Building And Exporting A Unity Project

## 9.1 Build Settings And Target Platforms

**File > Build Settings** (or **Build Profiles** in newer Unity versions) is where you choose a target platform and produce a standalone build — Unity's equivalent of Godot's export presets from the earlier Topic.

```
┌───────────────────────────────────┐
│  Build Settings                      │
├───────────────────────────────────┤
│  Scenes In Build:                    │
│   [x] MainMenu.unity                  │
│   [x] Level1.unity                    │
│   [ ] TestScene.unity                 │
├───────────────────────────────────┤
│  Platform:                            │
│   ▸ PC, Mac & Linux Standalone         │
│   ▸ Android                            │
│   ▸ iOS                                │
│   ▸ WebGL                              │
│                                        │
│  [Switch Platform]  [Build]           │
└───────────────────────────────────┘
```

Two things are easy to forget here and worth checking every time:

- **Scenes In Build** — only scenes explicitly added to this list (and checked) are included in the final build; a scene existing in your Project window isn't enough on its own. Scene 0 in this list is what loads first when the build launches.
- **Switching platforms** re-imports and re-compresses every asset for that platform's requirements, which can take a long time on a large project — plan for this rather than switching platforms casually mid-session.

Some platforms (iOS, consoles) require additional external tooling beyond Unity itself — for example, an iOS build still needs Xcode on a Mac to produce the final installable app, similar to Godot's iOS export requirements from the earlier Topic.

---

## 9.2 Player Settings

**Edit > Project Settings > Player** configures metadata and behavior for the built application, separate from any individual scene — comparable to Godot's Project Settings reviewed before export:

- **Company Name / Product Name** — used in file paths, window titles, and some platform store listings.
- **Icon** — the application icon shown by the OS/store.
- **Resolution and Presentation** — default window size, fullscreen mode, and (per-platform) orientation/aspect ratio handling.
- **Other Settings** — includes the **Scripting Backend** (Mono vs IL2CPP — IL2CPP compiles C# to native code and is required for most mobile/console platforms), **Api Compatibility Level**, and platform-specific permissions (camera, microphone, etc. on mobile).

Player Settings are platform-specific — switching the active build target in Build Settings reveals a different set of relevant options here, since a WebGL build and an Android build have genuinely different concerns (WebGL cares about compression format; Android cares about minimum API level and permissions).

---

## 9.3 Addressables And Asset Bundles

As a project grows, including every asset directly in the main build becomes impractical — large games need to load content on demand (streaming a level, downloading DLC, reducing initial download size). Unity's **Addressables** system (Package Manager > Addressables) is the modern, recommended solution:

```csharp
using UnityEngine;
using UnityEngine.AddressableAssets;

public class LevelLoader : MonoBehaviour
{
    public AssetReference levelPrefab;

    async void LoadLevel()
    {
        var handle = levelPrefab.InstantiateAsync();
        await handle.Task;
    }
}
```

Rather than referencing an asset directly (which bundles it into the main build permanently), Addressables mark assets to be built into separate, independently loadable bundles — loaded by an address (a string key) at runtime, only when actually needed. This is what enables patterns like downloadable content, reducing a mobile game's initial install size, or loading only the current level's assets instead of every level's assets at once.

**Asset Bundles** are the older, lower-level system Addressables is built on top of — for new projects, Addressables is the recommended starting point rather than working with raw Asset Bundles directly, since it handles dependency management and loading/unloading automatically.

---

## 9.4 Publishing To Stores

Where you publish depends entirely on your chosen platform, and each has its own separate submission process outside Unity's control:

| Platform | Store | Notes |
|---|---|---|
| **PC** | Steam, itch.io, Epic Games Store | Steam requires a developer fee and Steamworks SDK integration for achievements/cloud saves, similar to the Godot Topic's Steam notes; itch.io has no approval process, ideal for a first release |
| **Mobile** | Google Play (Android), Apple App Store (iOS) | Each requires a developer account, its own review process, and platform-specific requirements (privacy policy, content rating, signing) |
| **WebGL** | Any static web host, or itch.io's HTML5 support | WebGL builds are static files, similar in spirit to Godot's HTML5 export — must be served over HTTP(S) |
| **Console** | PlayStation, Xbox, Nintendo Switch | Requires a separate developer/publisher agreement with the platform holder, NDA-covered documentation, and formal certification — a meaningfully longer process than any of the above |

For a first shipped project, **itch.io** (desktop or WebGL) remains the lowest-friction path, exactly as recommended for the Godot Topic — it lets you validate the entire build-and-publish pipeline (Build Settings, Player Settings, testing an actual exported build rather than just Play Mode) before tackling a storefront with a formal review process.

---

This concludes the **Introduction to Unity** Topic. From here, the natural next step is building a small complete project — a simple platformer or top-down game — applying Lessons 3 through 8 together: GameObjects/Components/Prefabs for structure, C# and the MonoBehaviour lifecycle for logic, Physics/Colliders for interaction, UI for feedback, and the Animator for polish, before building and publishing it with Lesson 9.

[Previous](./[8]-Unity's-Animation-System-(Animator-And-Mecanim).md) | [Table of Contents](./[0]-Introduction-to-Unity.md)
