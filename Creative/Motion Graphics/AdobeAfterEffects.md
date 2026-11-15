[⬅ Back to Motion Graphics Fundamentals](./[0]-Introduction-to-MotionGraphics.md)

<div align="center">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/aftereffects/aftereffects-original.svg" width="50"/><br>
  <h1 style="margin-top: 0;">Adobe After Effects</h1>
</div>

Adobe After Effects is a timeline-based application for motion graphics, compositing, and visual effects. It is paid, subscription-based software for Windows and macOS (with a free trial) and integrates closely with other Adobe apps such as Premiere Pro, Photoshop, and Illustrator.

Website: [https://www.adobe.com/products/aftereffects.html](https://www.adobe.com/products/aftereffects.html)

---

## What Is After Effects?

After Effects animates layers, such as text, shapes, images, and video, over time. Where a video editor arranges clips in sequence, After Effects stacks layers on top of each other and animates their properties. It is widely used for title sequences, animated logos, lower thirds, explainer videos, and effects added on top of footage.

It is commonly used for:

- Animated logos and brand intros
- Title sequences and lower thirds
- Explainer and social media videos
- Compositing and green-screen work
- Visual effects such as tracking, rotoscoping, and particles

---

## Getting Started

1. Install **Adobe Creative Cloud** and download After Effects (a free trial is available).
2. Launch the app and choose **New Composition** from the start screen.
3. Set the composition resolution, frame rate, and duration, for example 1920 × 1080 at 30 fps for 5 seconds.
4. Import assets with **File → Import → File** (or drag them into the Project panel).

---

## The Interface

| Area | Purpose |
|---|---|
| **Project panel** | Stores imported footage, images, and compositions |
| **Composition panel** | Preview of your current composition |
| **Timeline panel** | Layers and their animated properties over time |
| **Tools bar** (top) | Selection, pen, text, shape, and puppet tools |
| **Effects & Presets** | Library of built-in effects and animation presets |
| **Properties / Character panels** | Edit properties and text styling |

---

## Key Concepts

- **Compositions** – the container that holds your layers, size, frame rate, and duration.
- **Layers** – every element in a composition (text, shape, image, video, solid, null, camera, or adjustment layer).
- **Keyframes** – mark a property's value at a point in time; After Effects fills in the motion between them.
- **Graph Editor** – fine-tune easing and speed using value and speed curves.
- **Parenting and null objects** – link layers so they move together, or control many layers with one handle.
- **Pre-compositions** – nest compositions inside one another to keep complex projects organized.
- **Effects and expressions** – built-in effects plus small scripts (for example `wiggle(2, 30)`) that automate motion.

---

## Useful Shortcuts

| Shortcut | Action |
|---|---|
| `P` / `S` / `R` / `T` | Show Position / Scale / Rotation / Opacity properties |
| `U` | Show all animated properties on selected layers |
| `F9` | Apply Easy Ease to selected keyframes |
| `Ctrl/Cmd + D` | Duplicate layer |
| `Ctrl/Cmd + Shift + C` | Pre-compose layers |
| `Spacebar` | Play / pause preview |
| `B` / `N` | Set work area start / end |

---

## Example Walkthrough

Animating a simple logo reveal:

1. Create a new **Composition** (1920 × 1080, 30 fps, 5 seconds).
2. Import your logo (an Illustrator `.ai` or SVG file works well) and drag it into the timeline.
3. Select the layer and press `S` to reveal Scale. Set a keyframe at 0 s with a value of 0%.
4. Move forward 1 second and set the scale to 100%.
5. Press `T` to reveal Opacity and animate it from 0% to 100% over the same period.
6. Select all keyframes and press `F9` to apply **Easy Ease**, then fine-tune in the **Graph Editor**.
7. Add a text layer below the logo and animate it into place with a short delay.
8. Choose **Composition → Add to Render Queue** (or Adobe Media Encoder) and export as H.264 MP4.

---

## Tips and Common Mistakes

- **Always use easing.** Linear motion looks mechanical and unnatural.
- **Organize layers and name them**, since timelines get crowded quickly.
- **Pre-compose** groups of layers to keep your main timeline readable.
- **Preview at lower resolution** (Half or Quarter) for smoother playback.
- **Mind your frame rate.** Match the composition to the project you'll deliver.

---

## When To Choose After Effects

Choose After Effects for animated graphics, compositing, and effects. For assembling and cutting longer footage, use an editor such as [Adobe Premiere Pro](../Video%20Production/AdobePremierePro.md) or [DaVinci Resolve](../Video%20Production/DaVinciResolve.md) (which also includes its own Fusion effects page).

---

## Where To Go Next

- Animate a short intro for a fictional YouTube channel.
- Learn the Graph Editor, as it is the biggest step toward professional-looking motion.
- Explore shape layers and trim paths for line-drawing animations.

[⬅ Back to Motion Graphics Fundamentals](./[0]-Introduction-to-MotionGraphics.md)