[⬅ Back to Video Production Fundamentals](./[0]-Introduction-to-VideoProduction.md)

<div align="center">
  <img src="https://cdn.jsdelivr.net/npm/simple-icons@latest/icons/davinciresolve.svg" width="50"/><br>
  <h1 style="margin-top: 0;">DaVinci Resolve</h1>
</div>

DaVinci Resolve is a video editing application from Blackmagic Design that combines editing, color grading, visual effects, motion graphics, and audio post-production in one program. It runs on Windows, macOS, and Linux, and has a free version as well as a paid Studio version.

Website: [https://www.blackmagicdesign.com/products/davinciresolve](https://www.blackmagicdesign.com/products/davinciresolve)

---

## What Is DaVinci Resolve?

Resolve is organized into "pages," each designed for one stage of production. You move through them from left to right as a project progresses, without exporting between separate apps. It began as a color grading system used in Hollywood and is still especially well known for its color tools, though its editing and audio features are now complete as well.

It is commonly used for:

- Editing videos of any length
- Professional color correction and grading
- Audio mixing and cleanup
- Visual effects and motion graphics
- Delivering to YouTube, social media, and broadcast formats

---

## Getting Started

1. Download DaVinci Resolve (free version) from the Blackmagic Design website.
2. Install and launch it, then create a new project from the Project Manager.
3. Open **File → Project Settings** and set the timeline resolution and frame rate to match your footage.
4. Import clips on the **Media** page using the Media Pool or by dragging files in.

---

## The Interface

| Area | Purpose |
|---|---|
| **Page tabs** (bottom) | Switch between Media, Cut, Edit, Fusion, Color, Fairlight, and Deliver |
| **Media Pool** | Stores and organizes imported clips |
| **Viewer** | Preview clips and the timeline |
| **Timeline** | Arrange clips and tracks |
| **Inspector** | Edit properties of the selected clip |
| **Scopes and color tools** | Waveforms, vectorscopes, wheels, and curves (Color page) |

---

## Key Concepts

- **Pages** – dedicated workspaces for each production stage.
- **Cut and Edit pages** – assemble and refine your timeline; Cut is faster and simpler, Edit is more detailed.
- **Color page** – node-based color correction and grading with primary wheels, curves, and secondary adjustments.
- **Nodes** – each node applies a specific adjustment, and nodes can be chained together.
- **Fusion page** – node-based visual effects and motion graphics.
- **Fairlight page** – professional audio mixing, EQ, and noise reduction.
- **Deliver page** – render and export your finished video with presets.

---

## Free vs Studio Version

| Feature | Free | Studio (Paid) |
|---|---|---|
| Editing, color, Fusion, Fairlight | ✔ | ✔ |
| Resolution limit | Up to UHD 4K | Up to 32K |
| AI tools (e.g., Magic Mask, noise reduction) | Limited | Expanded |
| Multi-GPU support | Limited | ✔ |

Feature availability can change between versions, so check the Blackmagic Design website for the current comparison.

---

## Useful Shortcuts

| Shortcut (Windows / macOS) | Action |
|---|---|
| `Ctrl/Cmd + B` | Blade (cut) at playhead |
| `Ctrl/Cmd + \` | Split clip |
| `I` / `O` | Set in point / out point |
| `J` / `K` / `L` | Play reverse / pause / play forward |
| `Shift + 4` to `Shift + 7` | Switch pages (Edit, Fusion, Color, etc.) |
| `Ctrl/Cmd + Z` | Undo |

---

## Example Walkthrough

Editing and finishing a short video:

1. Create a new project and import clips on the **Media** page.
2. Create a timeline and arrange the clips on the **Edit** page, trimming with the Blade tool.
3. Add b-roll on a second video track and music on an audio track.
4. Switch to the **Color** page, add a node, and adjust exposure and contrast with the color wheels.
5. Add a second node to refine skin tones or the sky using a qualifier or power window.
6. Switch to the **Fairlight** page and clean up dialogue with noise reduction and EQ.
7. Add titles from the **Effects** library on the Edit page.
8. Open the **Deliver** page, choose a preset such as YouTube or H.264, set the output location, and click **Add to Render Queue**, then **Render All**.

---

## Tips and Common Mistakes

- **Set the timeline settings first.** Changing them later can be awkward.
- **Use proxies or optimized media** if playback stutters on large files.
- **Save your project** regularly and use the Project Manager's backup options.
- **Learn nodes gradually.** Start with one or two before building complex trees.
- **Check your GPU.** Resolve relies heavily on graphics hardware for smooth performance.

---

## When To Choose DaVinci Resolve

Choose Resolve for color-focused projects, for an all-in-one workflow, or if you want a powerful free editor. If you already work heavily with other Adobe apps, [Adobe Premiere Pro](./AdobePremierePro.md) may fit better. For complex animated graphics, [Adobe After Effects](../Motion%20Graphics/AdobeAfterEffects.md) remains a popular companion.

---

## Where To Go Next

- Edit a short video start to finish using every page at least once.
- Practice color matching two clips shot under different lighting.
- Explore Fusion by building a simple animated title.

[⬅ Back to Video Production Fundamentals](./[0]-Introduction-to-VideoProduction.md)