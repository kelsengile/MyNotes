[Previous](./[8]-Unreal's-Animation-System-(Animation-Blueprints).md) | [Table of Contents](./[0]-Introduction-to-UnrealEngine.md)

*Shipping*

# Lesson 9 - Packaging And Deploying An Unreal Project

## 9.1 Project Settings Before Packaging

Before packaging, a handful of settings in **Edit > Project Settings** are worth confirming — several default to placeholder values that are easy to forget until a store submission rejects them:

| Setting | Location | Why It Matters |
|---|---|---|
| Project Name / Description | Description | Shown in some platform metadata and the default window title |
| Default Maps (Editor Startup Map, Game Default Map) | Maps & Modes | Which level actually loads when the packaged build launches — an easy thing to leave pointed at a test level |
| Icon / Splash Image | Platforms > Windows (or relevant platform) | Placeholder Unreal branding ships if left unset |
| Packaging > Build Configuration | Packaging | `Shipping` for release (optimized, no console/debug tools), `Development` for internal test builds that still need debugging access |

**List of Maps to Include** (also under Packaging) matters specifically for larger projects — by default Unreal tries to include every level under `Content/`, and explicitly listing only the maps actually used by the shipped game avoids bloating build size with test/prototype levels left in the project.

---

## 9.2 Packaging For A Platform

Packaging is started from **Platforms** (toolbar) or **Platforms > [Target Platform] > Package Project**, which compiles the project (if C++ classes are present), cooks content (9.3), and assembles everything into a distributable build for the chosen platform:

```
Platforms ▾
├── Windows        (requires no extra SDK for basic builds)
├── Mac / Linux
├── Android          (requires Android SDK/NDK setup)
├── iOS                (requires a Mac + Apple Developer account)
└── [Console platforms — require separate NDA'd SDKs from the platform holder]
```

Each non-desktop platform generally needs its SDK installed and configured first (checked under **Platforms > [Platform] > SDK status** in Project Settings, which flags missing components before a packaging attempt fails partway through). Packaging a large project can take anywhere from minutes to well over an hour depending on content volume and target platform, since it's compiling, cooking, and compressing the entire shipped asset set rather than just launching a quick preview like PIE (Lesson 2.1).

---

## 9.3 Cooking Content

**Cooking** converts the project's raw editor-only assets (uncompressed textures, editable Blueprints, source meshes) into platform-optimized runtime formats as part of packaging — compressed textures matching the target platform's GPU, precompiled shaders, and serialized binary versions of Blueprints and data assets:

```
Content/ (editor source assets)          Saved/Cooked/[Platform]/ (runtime-ready)
├── Meshes/SM_Wall.uasset                ├── Meshes/SM_Wall.uexp / .ubulk
├── Materials/M_Brick.uasset               ├── Materials/M_Brick (compiled shaders)
└── Blueprints/BP_Enemy.uasset              └── Blueprints/BP_Enemy (serialized)
```

This is a genuinely separate step from compiling C++ — a Blueprint-only project still needs to be cooked, since cooking is about asset format, not code. A **"By The Book"** cook (the default) only cooks maps and assets explicitly referenced starting from the maps listed in 9.1's packaging settings, which is why an unreferenced level or asset can silently be excluded from a build — a common cause of "it works in the Editor but not in the packaged build," worth checking first when a shipped build seems to be missing content that exists in the project.

---

## 9.4 Publishing To Stores

Once packaged, distribution specifics vary considerably by platform, but a few concepts are common across most of them:

- **Store-specific packaging requirements** — most storefronts (Steam, the Epic Games Store, mobile app stores, console storefronts) have their own required metadata, icon sizes, age-rating submissions, and signing/certificate steps layered on top of Unreal's own packaging output — these are platform requirements, not something Unreal Engine itself enforces.
- **Build signing** — mobile and console platforms typically require the packaged build to be cryptographically signed with a platform-issued certificate/key before a store will accept it; desktop platforms like Steam are more permissive but still commonly recommend code-signing to avoid OS/antivirus warnings.
- **DLC and content updates** — Unreal supports **Chunking** (splitting cooked content into separately-downloadable chunks via the Asset Manager) for projects that need post-launch content delivery without redistributing the entire game.

Because store requirements, SDK versions, and submission portals change independently of Unreal Engine's own release cycle, the authoritative source for any given platform's current submission process is always that platform's own developer documentation (Steamworks, the Epic Games Store dashboard, Google Play Console, App Store Connect, or the relevant console manufacturer's developer portal) rather than any general engine tutorial — Unreal's role ends at producing a correctly packaged, platform-ready build; everything past that point is the platform holder's process.

---

This concludes the Unreal Engine Topic. Together with the tool-agnostic ideas from Game Engine Fundamentals, you've now covered the Unreal Editor's interface, the Actor/Component architecture, both Blueprint and C++ scripting, physics and collision, UMG for UI, the animation system, and packaging a project for release — a solid foundation for continuing into Unreal's more specialized systems (networking, Niagara VFX, the Sequencer for cinematics) on your own.

[Previous](./[8]-Unreal's-Animation-System-(Animation-Blueprints).md) | [Table of Contents](./[0]-Introduction-to-UnrealEngine.md)