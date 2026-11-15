[⬅ Back to README](../../../README.md)

<div align="center">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/illustrator/illustrator-plain.svg" width="50"/><br>
  <h1 style="margin-top: 0;">Introduction to 2D Graphics</h1>
</div>

2D graphics is the craft of creating flat, two-dimensional images: logos, icons, illustrations, photo edits, posters, textures, and digital paintings. Nearly everything you see on a screen or a printed page started as 2D artwork at some point. This Topic covers the fundamentals behind that work: how images are stored, the difference between raster and vector, how layers, selections, and masks let you edit without destroying your original, and how to prepare finished artwork for the web or for print.

These lessons intentionally avoid tying the concepts to a single program. Once you understand the fundamentals here, head to the tool-specific files below to see how the same ideas show up in real, widely-used software.

### Why Learn 2D Graphics?

- **It's the foundation of visual work** — web design, game art, branding, social media, and print all build on the same core skills.
- **The concepts transfer between tools** — layers, masks, paths, and color modes work almost identically in every editor, so learning one makes the next easy.
- **It makes you a better collaborator** — knowing why a logo needs to be a vector, or why a photo looks blurry when enlarged, saves time and avoids rework with designers and developers.
- **You can start for free** — powerful tools like GIMP and Krita cost nothing, so there is no barrier to practicing.

A quick sense of the two main image types you will work with:

| Aspect | Raster (Bitmap) | Vector |
|---|---|---|
| How it's stored | Grid of colored pixels | Mathematical shapes, points, and curves |
| Scaling | Loses quality when enlarged | Stays sharp at any size |
| Best for | Photos, paintings, textures | Logos, icons, illustrations, typography |
| Typical formats | PNG, JPEG, WebP, PSD, XCF | SVG, AI, EPS, PDF |
| Example tools | GIMP, Krita | Adobe Illustrator |

### Tools:

The lessons below teach the fundamentals that apply across all 2D software. To see those fundamentals applied to specific tools, continue on to:

<table align="center" width="100%" style="margin-left:auto;margin-right:auto;table-layout:fixed;"> <tr><th align="center" width="25%">Tool</th><th align="center" width="75%">Description</th></tr> <tr> <td align="center" width="25%" height="100"><a href="./AdobeIllustrator.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/illustrator/illustrator-plain.svg" width="40" title="Adobe Illustrator"/><br>Adobe Illustrator</a></td> <td align="left" width="75%">Industry-standard vector editor for logos, icons, typography, and scalable illustrations.</td> </tr> <tr> <td align="center" width="25%" height="100"><a href="./Gimp.md"><img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/gimp/gimp-original.svg" width="40" title="GIMP"/><br>GIMP</a></td> <td align="left" width="75%">Free, open-source raster editor for photo retouching, compositing, and general image editing.</td> </tr> <tr> <td align="center" width="25%" height="100"><a href="./Krita.md"><img src="https://cdn.jsdelivr.net/npm/simple-icons@latest/icons/krita.svg" width="40" title="Krita"/><br>Krita</a></td> <td align="left" width="75%">Free painting program built for digital artists, illustrators, comic creators, and concept artists.</td> </tr> </table>

---

### Table of Contents

**Basics**

   1. **[What Is 2D Graphics](#lesson-1---what-is-2d-graphics)**  
       1.1 Defining 2D Graphics  
       1.2 Raster vs Vector  
       1.3 Pixels, Resolution, and DPI  
       1.4 Color Modes (RGB, CMYK, Grayscale)  
   2. **[Common File Formats](#lesson-2---common-file-formats)**  
       2.1 Lossy vs Lossless Compression  
       2.2 Raster Formats (PNG, JPEG, WebP)  
       2.3 Vector Formats (SVG, PDF, EPS)  
       2.4 Working Files vs Export Files  
   3. **[Layers Selections And Masks](#lesson-3---layers-selections-and-masks)**  
       3.1 How Layers Work  
       3.2 Blend Modes and Opacity  
       3.3 Selections and Paths  
       3.4 Masks and Non-Destructive Editing  

**Practice**

   4. **[Drawing Painting And Editing](#lesson-4---drawing-painting-and-editing)**  
       4.1 Brushes and Pressure Sensitivity  
       4.2 Color Theory Basics  
       4.3 Photo Retouching Techniques  
       4.4 Vector Shapes and the Pen Tool  
   5. **[Composition And Typography](#lesson-5---composition-and-typography)**  
       5.1 Layout and the Rule of Thirds  
       5.2 Contrast, Balance, and Hierarchy  
       5.3 Choosing and Pairing Fonts  
       5.4 Working With Grids and Guides  

**Ecosystem**

   6. **[Exporting And Delivering Artwork](#lesson-6---exporting-and-delivering-artwork)**  
       6.1 Exporting for Web  
       6.2 Preparing Files for Print  
       6.3 Optimizing File Size  
       6.4 Organizing and Naming Assets  
   7. **[Choosing A 2D Tool](#lesson-7---choosing-a-2d-tool)**  
       7.1 Matching Tools to Tasks  
       7.2 Free vs Paid Software  
       7.3 Building a Practice Routine  
       7.4 Where to Go Next  

---

*Basics*

## Lesson 1 - What Is 2D Graphics

### 1.1 Defining 2D Graphics

2D graphics are images with only width and height: no depth, no camera, and no perspective that changes as you move. A phone photo, a website logo, and a comic page are all 2D graphics, and they are the starting point for almost every visual project.

```mermaid
flowchart LR
    A["2D Graphics<br/>(width + height only)"] --> B["Logos & Icons"]
    A --> C["Photos & Edits"]
    A --> D["Illustrations & Comics"]
    A --> E["Posters & Textures"]
```

```
        Width (X)
   ┌─────────────────►
   │  ┌───────────┐
 H │  │           │      Only two axes:
 e │  │   IMAGE   │      no Z (depth), no camera.
 i │  │           │
 g │  └───────────┘
 h ▼
 t (Y)
```

---

### 1.2 Raster vs Vector

Raster images are grids of colored pixels. They look natural for photos and paintings but blur when enlarged because the pixels just get bigger. Vector images store shapes as math (points, curves, fills), so they stay sharp at any size, which makes them ideal for logos and icons.

**Visual comparison:** the same diagonal line, stored two ways.

```
 RASTER (pixels)                 VECTOR (math)

 ■ □ □ □ □ □                     Line from (0,0) to (5,5)
 □ ■ □ □ □ □                     stroke: #222, width: 1
 □ □ ■ □ □ □
 □ □ □ ■ □ □         zoom ×4     Zoom in: the formula is simply
 □ □ □ □ ■ □        ───────►     recalculated, so edges stay smooth.
 □ □ □ □ □ ■
 Zoom in: blocky "staircase"
```

**Sample:** a complete vector graphic is just a few lines of text (SVG).

```svg
<svg xmlns="http://www.w3.org/2000/svg" width="120" height="120" viewBox="0 0 120 120">
  <circle cx="60" cy="60" r="50" fill="#4f46e5"/>
  <rect x="35" y="35" width="50" height="50" rx="8" fill="#ffffff"/>
</svg>
```

---

### 1.3 Pixels, Resolution, and DPI

Resolution is the number of pixels in an image, such as 1920 × 1080. DPI (dots per inch) describes how densely pixels are printed on paper. Around 72–96 DPI is typical for screens, and 300 DPI is the usual print target, so print images need far more pixels than screen images of the same physical size.

> [!TIP]
> **Pixels needed = print size (inches) × DPI**

| Print size | At 72 DPI (screen) | At 300 DPI (print) |
|---|---|---|
| 4 × 6 in (photo) | 288 × 432 px | 1200 × 1800 px |
| 8.5 × 11 in (letter) | 612 × 792 px | 2550 × 3300 px |
| 11 × 17 in (poster) | 792 × 1224 px | 3300 × 5100 px |

---

### 1.4 Color Modes (RGB, CMYK, Grayscale)

RGB mixes red, green, and blue light and is used for screens. CMYK mixes cyan, magenta, yellow, and black ink and is used for print. Grayscale uses only shades between black and white. Choose the mode before you start, since converting later (especially RGB to CMYK) can shift colors.

```mermaid
flowchart LR
    subgraph RGB["RGB — Additive (light)"]
      R["Red"] --- G["Green"] --- B["Blue"]
    end
    subgraph CMYK["CMYK — Subtractive (ink)"]
      C["Cyan"] --- M["Magenta"] --- Y["Yellow"] --- K["Black (Key)"]
    end
    RGB -->|"Screens, web, apps"| S["Display"]
    CMYK -->|"Printers, posters"| P["Paper"]
```

| Mode | Combine all colors | Gamut | Use for |
|---|---|---|---|
| RGB | White | Wide (vivid) | Web, apps, video |
| CMYK | Muddy dark brown/black | Narrower | Print |
| Grayscale | n/a | 256 gray levels | B/W photos, manga, scans |

---

*Basics*

## Lesson 2 - Common File Formats

### 2.1 Lossy vs Lossless Compression

Lossless formats shrink a file without discarding data, so the image can be restored exactly. Lossy formats throw away some detail for much smaller files, and quality drops a little more every time you re-save. Use lossless for graphics and masters, and lossy when size matters more than perfect detail.

```mermaid
flowchart LR
    M["Master<br/>(100% quality)"] -->|"Save as JPEG"| J1["Copy 1<br/>~92%"]
    J1 -->|"Edit + re-save"| J2["Copy 2<br/>~85%"]
    J2 -->|"Edit + re-save"| J3["Copy 3<br/>~78%"]
    M -->|"Save as PNG"| P["Identical to master<br/>(lossless)"]
```

*(Percentages are illustrative: the point is that lossy quality only goes down.)*

---

### 2.2 Raster Formats (PNG, JPEG, WebP)

PNG is lossless and supports transparency, which suits graphics and screenshots. JPEG is lossy and compact, which suits photographs. WebP can work in either mode and is usually smaller than both, which is why it is popular on the web.

```mermaid
flowchart TD
    Q["What kind of image?"] --> A{"Needs transparency<br/>or sharp text?"}
    A -- Yes --> PNG["PNG (or lossless WebP)"]
    A -- No --> B{"Is it a photo?"}
    B -- Yes --> C{"Modern browsers only?"}
    C -- Yes --> WEBP["WebP"]
    C -- No --> JPEG["JPEG"]
    B -- No --> PNG
```

| Format | Compression | Transparency | Animation |
|---|---|---|---|
| PNG | Lossless | ✅ | ❌ |
| JPEG | Lossy | ❌ | ❌ |
| WebP | Both | ✅ | ✅ |

---

### 2.3 Vector Formats (SVG, PDF, EPS)

SVG is the standard vector format for the web and can be styled and animated with code. PDF can hold vector artwork and is widely used for print and sharing. EPS is an older print-oriented format you will still meet in some professional workflows.

**Sample:** an SVG icon styled and animated with CSS.

```html
<svg viewBox="0 0 24 24" width="48" height="48">
  <path class="heart" d="M12 21s-7-4.5-9.5-9A5.5 5.5 0 0 1 12 6a5.5 5.5 0 0 1 9.5 6c-2.5 4.5-9.5 9-9.5 9z"/>
</svg>

<style>
  .heart { fill: crimson; transition: transform .2s; transform-origin: center; }
  .heart:hover { transform: scale(1.2); }
</style>
```

| Format | Best for | Editable in code? |
|---|---|---|
| SVG | Web icons, logos, illustrations | ✅ Yes |
| PDF | Print-ready artwork, sharing | ❌ No |
| EPS | Legacy print workflows | ❌ No |

---

### 2.4 Working Files vs Export Files

Working files such as `.psd`, `.xcf`, `.kra`, and `.ai` keep layers, masks, and editable data. Export files such as PNG, JPEG, and SVG are the finished versions you share. Always keep the working file as your master copy, because exports are much harder to edit.

```mermaid
flowchart LR
    W["Working file<br/>.psd / .xcf / .kra / .ai<br/>(layers, masks, editable)"] -->|"Export"| E1["logo.png"]
    W -->|"Export"| E2["logo.svg"]
    W -->|"Export"| E3["banner.jpg"]
    E1 -. "hard to edit back" .-> W
```

---

*Basics*

## Lesson 3 - Layers Selections And Masks

### 3.1 How Layers Work

Layers are like transparent sheets stacked on top of each other. Each can be moved, edited, hidden, or deleted without touching the others, so mistakes are easy to undo and elements are easy to rearrange.

```
 ┌────────────────────────────┐  ▲ top (seen first)
 │ 👁  Text: "SUMMER SALE"     │  │
 ├────────────────────────────┤  │
 │ 👁  Shape: Orange circle    │  │  Layers are read
 ├────────────────────────────┤  │  top → bottom
 │ 👁  Photo: Beach            │  │
 ├────────────────────────────┤  │
 │ 👁  Background: Sky blue    │  ▼ bottom
 └────────────────────────────┘
 Hide any 👁 to remove that layer without erasing it.
```

---

### 3.2 Blend Modes and Opacity

Opacity controls how see-through a layer is. Blend modes such as Multiply, Screen, and Overlay control how a layer's colors combine with the layers below, which is useful for shadows, highlights, and textures.

| Blend mode | Effect | Typical use |
|---|---|---|
| Normal | Layer simply covers what's below | Default |
| Multiply | Darkens (white disappears) | Shadows, line art over color |
| Screen | Lightens (black disappears) | Glows, light leaks |
| Overlay | Boosts contrast | Textures, punchy color |
| Soft Light | Gentle version of Overlay | Subtle highlights |

```
 Opacity:  100%        50%         20%
           ████        ▓▓▓▓        ░░░░
          (solid)   (see-through)  (faint)
```

---

### 3.3 Selections and Paths

A selection limits your edits to part of the image, using tools like rectangle, lasso, or magic wand. A path is a vector outline that can be reshaped precisely and converted into a selection when you need a clean edge.

```mermaid
flowchart LR
    P["Draw a path<br/>(precise vector outline)"] --> S["Convert to selection<br/>(marching ants)"]
    S --> E["Edit only inside:<br/>fill, adjust, cut out"]
    E --> M["Save as mask<br/>for later changes"]
```

| Tool | Best for |
|---|---|
| Rectangle / Ellipse | Simple shapes |
| Lasso | Rough freehand areas |
| Magic wand | Areas of similar color |
| Pen / path tool | Clean, precise edges |

---

### 3.4 Masks and Non-Destructive Editing

A mask hides parts of a layer without erasing them: painting black hides and painting white reveals. Combined with duplicated layers and adjustment layers, masks let you change your mind at any time without losing original pixels.

```
 Layer            Mask                Result
 ┌──────────┐    ┌──────────┐       ┌──────────┐
 │ ████████ │    │ ██████░░ │       │ ██████   │
 │ ████████ │ +  │ ██████░░ │   =   │ ██████   │
 │ ████████ │    │ ██████░░ │       │ ██████   │
 └──────────┘    └──────────┘       └──────────┘
                  ██ white = show     (right edge hidden,
                  ░░ black = hide      but NOT deleted)
```

> [!TIP]
> **Mnemonic:** *"White reveals, black conceals."* Gray gives partial transparency, which is perfect for soft blends.

---

*Practice*

## Lesson 4 - Drawing Painting And Editing

### 4.1 Brushes and Pressure Sensitivity

Brushes control the shape, texture, and flow of your strokes. With a pen tablet, pressure sensitivity lets stroke width and opacity respond to how hard you press, which makes lines feel more natural.

```
 Light pressure ─────────────────────► Heavy pressure
   ·  ·  ˙  .  ,  :  ;  ▪  ●  ⬤
 thin, faint                  thick, solid
```

| Brush setting | What it controls | Good starting value |
|---|---|---|
| Size | Stroke width | Varies with pressure |
| Opacity | Transparency of each stroke | 100% for inking, 30–60% for shading |
| Hardness | Edge softness | 100% crisp, 0% airbrush |
| Spacing | Gap between "dabs" | 5–10% for smooth lines |

---

### 4.2 Color Theory Basics

Hue is the color itself, saturation is its intensity, and value is how light or dark it is. Harmonies such as complementary and analogous palettes help colors work together, and strong value contrast keeps artwork readable.

| Harmony | Idea | Example palette |
|---|---|---|
| Complementary | Opposites on the color wheel | `#2563eb` blue + `#f59e0b` orange |
| Analogous | Neighbors on the wheel | `#22c55e` + `#14b8a6` + `#06b6d4` |
| Triadic | Three evenly spaced hues | `#ef4444` + `#3b82f6` + `#eab308` |
| Monochrome | One hue, many values | `#dbeafe` → `#60a5fa` → `#1e3a8a` |

```
 Value scale (light → dark)
 ░░░░  ▒▒▒▒  ▓▓▓▓  ████
  10%   35%   65%   95%      Check readability by converting
                             your art to grayscale.
```

---

### 4.3 Photo Retouching Techniques

Common retouching steps include cropping, adjusting exposure and curves, correcting white balance, and removing blemishes with clone or heal tools. Work on a duplicate layer so the original stays untouched.

```mermaid
flowchart LR
    A["Duplicate<br/>original layer"] --> B["Crop &<br/>straighten"]
    B --> C["Exposure &<br/>curves"]
    C --> D["White balance<br/>& color"]
    D --> E["Heal / clone<br/>blemishes"]
    E --> F["Sharpen &<br/>export"]
```

---

### 4.4 Vector Shapes and the Pen Tool

Vector work starts with basic shapes that are combined and subtracted into new forms. The pen tool draws precise curves by placing anchor points and adjusting handles. It takes practice, but it is the core vector skill.

```
        handle ○─────●─────○ handle       ● anchor point
                    / \                   ○ handle (controls curve)
          ╭────────╯   ╰────────╮
      ●──╯                       ╰──●
   Longer handle = wider curve. Shorter = tighter curve.
```

**Sample:** the same curve written as an SVG path (`M` move, `C` cubic curve).

```svg
<path d="M 10 80 C 40 10, 65 10, 95 80" stroke="#4f46e5" stroke-width="3" fill="none"/>
```

| Shape operation | Result |
|---|---|
| Unite | Merge shapes into one |
| Subtract | Cut one shape out of another |
| Intersect | Keep only the overlap |
| Exclude | Keep everything except the overlap |

---

*Practice*

## Lesson 5 - Composition And Typography

### 5.1 Layout and the Rule of Thirds

Composition is how elements are arranged in the frame. The rule of thirds divides the canvas into a 3 × 3 grid, and placing key elements on the lines or intersections usually feels more balanced than centering everything.

```
 ┌───────────┬───────────┬───────────┐
 │           │           │           │
 │           │           │    ◎ ◄────┼── subject on a power point
 ├───────────┼───────────┼───────────┤
 │           │           │           │
 │           │           │           │
 ├───────────┼───────────┼───────────┤
 │ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~ │ ◄─ horizon on a line
 └───────────┴───────────┴───────────┘
```

---

### 5.2 Contrast, Balance, and Hierarchy

Contrast in size, color, or weight draws the eye. Balance spreads visual weight across the layout. Hierarchy makes sure viewers notice the most important element first, then the next.

```
 ┌──────────────────────────────┐
 │  ██████████████████  ◄─ 1st: headline (big, bold)
 │  ▓▓▓▓▓▓▓▓▓▓▓         ◄─ 2nd: subheading
 │  ░░░░░░░░░░░░░░░░    ◄─ 3rd: body text
 │  ░░░░░░░░░░░░░
 │              [ BUTTON ] ◄─ call to action (contrast color)
 └──────────────────────────────┘
```

| Tool | How to apply |
|---|---|
| Size | Bigger = more important |
| Color | Bright accent against neutral surroundings |
| Weight | Bold vs regular |
| Whitespace | Space around an element makes it stand out |

---

### 5.3 Choosing and Pairing Fonts

Pick fonts that suit the tone of the design and stay readable at small sizes. Pairing one serif with one sans-serif, or using two weights of the same family, is a safe way to avoid clashing styles.

| Heading font | Body font | Feel |
|---|---|---|
| Playfair Display (serif) | Source Sans 3 (sans) | Elegant, editorial |
| Montserrat (sans) | Merriweather (serif) | Modern, friendly |
| Inter Bold | Inter Regular | Clean, techy (one family) |
| Bebas Neue | Open Sans | Bold, poster-like |

> [!TIP]
> Limit yourself to **two typefaces** and **three sizes** (heading, body, caption) until you are confident.

---

### 5.4 Working With Grids and Guides

Grids and guides align elements precisely and keep spacing consistent. Using the same margins and column widths across a design makes it look deliberate and tidy.

```
  margin                                        margin
  ◄──►┌──────┐ ┌──────┐ ┌──────┐ ┌──────┐┌──►
      │ col1 │ │ col2 │ │ col3 │ │ col4 │
      │      │ │      │ │      │ │      │
      │ ████ │ │ ████ │ │ ████ │ │ ████ │   gutter = equal gap
      └──────┘ └──────┘ └──────┘ └──────┘   between columns
```

---

*Ecosystem*

## Lesson 6 - Exporting And Delivering Artwork

### 6.1 Exporting for Web

Use RGB color, a sensible pixel size, and a compact format such as JPEG, PNG, WebP, or SVG. Export at 2× size when you need sharp display on high-density screens.

| Display size | Export @1× | Export @2× (retina) |
|---|---|---|
| 300 × 200 | 300 × 200 | 600 × 400 |
| 1200 × 630 (social card) | 1200 × 630 | 2400 × 1260 |

**Sample:** serving both sizes in HTML.

```html
<img src="hero.jpg"
     srcset="hero.jpg 1x, hero@2x.jpg 2x"
     alt="Product hero image" width="600" height="400">
```

---

### 6.2 Preparing Files for Print

Use CMYK and 300 DPI, and add bleed (extra artwork past the trim edge) so cuts don't leave white borders. Convert text to outlines or embed fonts so nothing changes at the printer.

```
 ┌─────────────────────────────┐ ◄ bleed edge (+3 mm, artwork runs here)
 │ ┌─────────────────────────┐ │
 │ │ ┌─────────────────────┐ │ │ ◄ trim line (final cut)
 │ │ │                     │ │ │
 │ │ │   SAFE AREA         │ │ │ ◄ keep text & logos inside
 │ │ │   (important stuff) │ │ │   (about 3–5 mm from trim)
 │ │ └─────────────────────┘ │ │
 │ └─────────────────────────┘ │
 └─────────────────────────────┘
```

> [!NOTE]
> ☐ CMYK mode  ☐ 300 DPI  ☐ 3 mm bleed  ☐ Fonts outlined/embedded  ☐ Crop marks (if requested)

---

### 6.3 Optimizing File Size

Smaller files load faster. Resize images to the dimensions you actually need, choose the right format, and raise compression until quality loss is just barely noticeable.

```mermaid
flowchart LR
    A["Original<br/>4000×3000 · 6 MB"] --> B["Resize to<br/>needed dimensions"]
    B --> C["Pick the right<br/>format"]
    C --> D["Compress<br/>(quality ~75–85)"]
    D --> E["Final<br/>1200×900 · 150 KB"]
```

---

### 6.4 Organizing and Naming Assets

Use clear, consistent file names (for example `logo-primary-dark.svg`) and keep source files, exports, and references in separate folders. Future you and your teammates will be grateful.

```
 brand-project/
 ├── 01-source/          ← working files (.psd, .ai, .kra)
 │   └── logo.ai
 ├── 02-exports/
 │   ├── web/
 │   │   ├── logo-primary-dark.svg
 │   │   └── logo-primary-light.svg
 │   └── print/
 │       └── logo-primary-cmyk.pdf
 └── 03-reference/       ← mood boards, briefs
```

---

*Ecosystem*

## Lesson 7 - Choosing A 2D Tool

### 7.1 Matching Tools to Tasks

Use a vector editor for logos and icons, a raster editor for photos and compositing, and a painting program for illustration. Many projects combine more than one.

```mermaid
flowchart TD
    S["What are you making?"] --> A{"Needs to scale<br/>to any size?"}
    A -- "Yes (logo, icon)" --> V["Vector editor<br/>e.g. Illustrator"]
    A -- No --> B{"Editing a photo?"}
    B -- Yes --> R["Raster editor<br/>e.g. GIMP"]
    B -- No --> P["Painting program<br/>e.g. Krita"]
```

---

### 7.2 Free vs Paid Software

Free tools like GIMP and Krita are fully capable for learning and for many professional jobs. Paid tools like Illustrator add polish, ecosystem integration, and industry-standard file compatibility.

| Tool | Cost | Type | Strongest at |
|---|---|---|---|
| GIMP | Free | Raster | Photo editing, compositing |
| Krita | Free | Raster (painting) | Digital painting, comics |
| Adobe Illustrator | Paid | Vector | Logos, icons, typography |

---

### 7.3 Building a Practice Routine

Short, regular practice beats occasional marathons. Try daily sketches, small recreation exercises, and weekly mini-projects that focus on one skill at a time.

| Day | 20–30 min exercise |
|---|---|
| Mon | Draw 5 simple shapes and combine them into an icon |
| Tue | Color study: recreate a palette from a photo |
| Wed | Retouch one photo using a duplicate layer |
| Thu | Typography: pair two fonts on a poster mock |
| Fri | Recreate a logo you admire |
| Weekend | Mini-project: combine the week's skills |

---

### 7.4 Where to Go Next

Pick a tool from the table above, work through its walkthrough, then recreate something you admire. From there, explore the Interfaces and Motion Graphics topics to see how 2D artwork moves into apps and animation.

```mermaid
flowchart LR
    F["Fundamentals<br/>(this topic)"] --> T["Pick a tool<br/>Illustrator · GIMP · Krita"]
    T --> R["Recreate<br/>something you admire"]
    R --> I["Interfaces"]
    R --> M["Motion Graphics"]
```