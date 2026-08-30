[⬅ Back to README](../../../README.md)

# CSS

Welcome! This is a self-paced course for learning CSS (Cascading Style Sheets), the language that styles and lays out every visual aspect of the web — from colors and typography to responsive, animated, production-grade interfaces.

---

## What is CSS?

CSS lets you:
- Style colors, typography, spacing, and visual appearance of HTML content
- Lay out pages with Flexbox, Grid, and positioning systems
- Build fully responsive designs that adapt to any screen size
- Create animations, transitions, and interactive visual effects
- Theme and reuse styles with custom properties (variables)
- Organize large-scale stylesheets with naming methodologies and architecture
- Enhance styles with preprocessors and modern tooling (Sass, PostCSS)
- Style print layouts, dark/light themes, and accessibility preferences
- Optimize rendering performance and maintainability of visual styles

## Table of Contents

**Getting Started**  

1. **[What is CSS & How Styling Works](./[1]-What-is-CSS-and-How-Styling-Works.md)**  
    1.1 What Is CSS?  
    1.2 Why "Cascading"?  
    1.3 How The Browser Applies Styles  
    1.4 What You Can Style With CSS  
2. **[Adding CSS: Inline, Internal & External Stylesheets](./[2]-Adding-CSS.md)**  
    2.1 Inline Styles  
    2.2 Internal Stylesheets  
    2.3 External Stylesheets  
    2.4 Choosing The Right Method  
3. **[CSS Syntax: Rules, Selectors, Properties & Values](./[3]-CSS-Syntax.md)**  
    3.1 Anatomy Of A CSS Rule  
    3.2 Multiple Declarations And Selectors  
    3.3 Comments  
    3.4 Whitespace And Formatting  
4. **[The Cascade, Specificity & Inheritance](./[4]-Cascade-Specificity-and-Inheritance.md)**  
    4.1 The Cascade  
    4.2 Specificity  
    4.3 `!important`  
    4.4 Inheritance  

**Selectors**  

5. **[Basic Selectors (element, class, ID, universal)](./[5]-Basic-Selectors.md)**  
    5.1 Element (Type) Selector  
    5.2 Class Selector  
    5.3 ID Selector  
    5.4 Universal Selector  
    5.5 Grouping Selectors  
6. **[Combinators (descendant, child, sibling)](./[6]-Combinators.md)**  
    6.1 Descendant Combinator (space)  
    6.2 Child Combinator (`>`)  
    6.3 Adjacent Sibling Combinator (`+`)  
    6.4 General Sibling Combinator (`~`)  
    6.5 Combining Combinators  
7. **[Attribute Selectors](./[7]-Attribute-Selectors.md)**  
    7.1 Presence Selector  
    7.2 Exact Value Selector  
    7.3 Partial And Pattern Matching  
    7.4 Case-Insensitive Matching  
    7.5 Practical Use Cases  
8. **[Pseudo-Classes (`:hover`, `:nth-child`, `:focus`, `:not`, etc.)](./[8]-Pseudo-Classes.md)**  
    8.1 Interaction States  
    8.2 Structural Pseudo-Classes  
    8.3 Form And Input States  
    8.4 The Negation Pseudo-Class: `:not()`  
    8.5 Other Useful Pseudo-Classes  
9. **[Pseudo-Elements (`::before`, `::after`, `::first-line`, etc.)](./[9]-Pseudo-Elements.md)**  
    9.1 `::before` And `::after`  
    9.2 `::first-line` And `::first-letter`  
    9.3 `::selection`  
    9.4 `::placeholder`  
    9.5 Pseudo-Classes Vs. Pseudo-Elements  

**The Box Model**  

10. **[The Box Model: Content, Padding, Border & Margin](./[10]-The-Box-Model.md)**  
    10.1 Every Element Is A Box  
    10.2 Content  
    10.3 Padding  
    10.4 Border  
    10.5 Margin  
    10.6 Total Element Size  
11. **[Box Sizing (`box-sizing: border-box`)](./[11]-Box-Sizing.md)**  
    11.1 The Default: `content-box`  
    11.2 The Alternative: `border-box`  
    11.3 The Universal Border-Box Reset  
    11.4 Why It Matters For Layout  
12. **[Margin Collapsing](./[12]-Margin-Collapsing.md)**  
    12.1 What Is Margin Collapsing?  
    12.2 When Collapsing Happens  
    12.3 When Collapsing Does *Not* Happen  
    12.4 Avoiding Unexpected Collapsing  
13. **[Display Property (block, inline, inline-block, none, contents)](./[13]-Display-Property.md)**  
    13.1 `block`  
    13.2 `inline`  
    13.3 `inline-block`  
    13.4 `none`  
    13.5 `contents`  
    13.6 Layout-Defining Values  

**Colors, Units & Typography**  

14. **[Color Formats (hex, rgb, hsl, named colors, alpha)](./[14]-Color-Formats.md)**  
    14.1 Named Colors  
    14.2 Hexadecimal  
    14.3 RGB And RGBA  
    14.4 HSL And HSLA  
    14.5 The `currentColor` Keyword  
    14.6 Choosing A Format  
15. **[Units: Absolute vs. Relative (px, em, rem, %, vw, vh)](./[15]-Units.md)**  
    15.1 Absolute Units: `px`  
    15.2 Relative Unit: `em`  
    15.3 Relative Unit: `rem`  
    15.4 Percentage: `%`  
    15.5 Viewport Units: `vw` And `vh`  
    15.6 Choosing The Right Unit  
16. **[Typography: Fonts, Font Faces & Web Fonts (`@font-face`, Google Fonts)](./[16]-Typography-and-Web-Fonts.md)**  
    16.1 The `font-family` Property  
    16.2 Font Weight And Style  
    16.3 System Fonts  
    16.4 Custom Fonts With `@font-face`  
    16.5 Google Fonts  
    16.6 Variable Fonts  
17. **[Text Styling (line-height, letter-spacing, text-align, text-decoration)](./[17]-Text-Styling.md)**  
    17.1 `line-height`  
    17.2 `letter-spacing` And `word-spacing`  
    17.3 `text-align`  
    17.4 `text-decoration`  
    17.5 `text-transform`  
    17.6 Overflow And Truncation  
    17.7 Word Breaking  
18. **[Backgrounds (color, image, gradient, position, size, repeat)](./[18]-Backgrounds.md)**  
    18.1 `background-color`  
    18.2 `background-image`  
    18.3 `background-repeat`  
    18.4 `background-position`  
    18.5 `background-size`  
    18.6 The `background` Shorthand  
    18.7 Gradients  
19. **[Borders, Shadows & Outlines (`box-shadow`, `border-radius`)](./[19]-Borders-Shadows-and-Outlines.md)**  
    19.1 `border-radius`  
    19.2 `box-shadow`  
    19.3 `text-shadow`  
    19.4 `outline`  
    19.5 Border Vs. Outline Vs. Box-Shadow  

**Layout Fundamentals**  

20. **[Normal Flow & Document Flow](./[20]-Normal-Flow.md)**  
    20.1 What Is Normal Flow?  
    20.2 Block Flow Direction  
    20.3 Inline Flow Direction  
    20.4 Taking Elements Out Of Flow  
    20.5 Why This Matters  
21. **[Positioning (static, relative, absolute, fixed, sticky)](./[21]-Positioning.md)**  
    21.1 `static` (The Default)  
    21.2 `relative`  
    21.3 `absolute`  
    21.4 `fixed`  
    21.5 `sticky`  
    21.6 The `z-index` Connection  
22. **[Floats & Clearing (legacy layout)](./[22]-Floats-and-Clearing.md)**  
    22.1 What `float` Does  
    22.2 Floats For Layout (Historical Context)  
    22.3 The Collapsing Parent Problem  
    22.4 The `clear` Property  
    22.5 The Clearfix Hack  
    22.6 Modern Alternative  
23. **[Z-index & Stacking Contexts](./[23]-Z-index-and-Stacking-Contexts.md)**  
    23.1 What `z-index` Does  
    23.2 What Is A Stacking Context?  
    23.3 What Creates A New Stacking Context  
    23.4 Debugging Z-Index Issues  

**Flexbox**  

24. **[Flexbox Fundamentals (flex container & items)](./[24]-Flexbox-Fundamentals.md)**  
    24.1 Creating A Flex Container  
    24.2 The Two Axes  
    24.3 Container Vs. Item Properties  
    24.4 Why Flexbox?  
25. **[Flex Direction, Wrap & Alignment](./[25]-Flex-Direction-Wrap-and-Alignment.md)**  
    25.1 `flex-direction`  
    25.2 `flex-wrap`  
    25.3 `justify-content` (Main Axis Alignment)  
    25.4 `align-items` (Cross Axis Alignment)  
    25.5 `align-content` (Multi-Line Alignment)  
    25.6 `gap`  
26. **[Flex Grow, Shrink & Basis](./[26]-Flex-Grow-Shrink-and-Basis.md)**  
    26.1 `flex-basis`  
    26.2 `flex-grow`  
    26.3 `flex-shrink`  
    26.4 The `flex` Shorthand  
    26.5 Practical Pattern: Equal-Width Columns  
    26.6 Practical Pattern: Fixed Sidebar + Fluid Content  
27. **[Common Flexbox Layout Patterns](./[27]-Common-Flexbox-Patterns.md)**  
    27.1 Perfect Centering  
    27.2 Navigation Bar  
    27.3 Sticky Footer  
    27.4 Equal-Height Cards  
    27.5 Responsive Wrapping Grid Of Cards  
    27.6 Media Object (Image + Text Side By Side)  

**CSS Grid**  

28. **[Grid Fundamentals (grid container & items)](./[28]-Grid-Fundamentals.md)**  
    28.1 Creating A Grid Container  
    28.2 One-Dimensional Vs. Two-Dimensional  
    28.3 The Grid Line Concept  
    28.4 Container Vs. Item Properties  
    28.5 `gap`  
29. **[Defining Rows, Columns & Grid Template Areas](./[29]-Grid-Rows-Columns-and-Template-Areas.md)**  
    29.1 `grid-template-columns` And `grid-template-rows`  
    29.2 The `fr` Unit  
    29.3 The `repeat()` Function  
    29.4 `grid-template-areas`  
    29.5 Implicit Rows And Columns  
30. **[Grid Alignment & Placement (`justify-items`, `align-self`, `grid-column`)](./[30]-Grid-Alignment-and-Placement.md)**  
    30.1 Placing Items By Line Number  
    30.2 The `grid-column`/`grid-row` Shorthand  
    30.3 `justify-items` And `align-items`  
    30.4 `justify-self` And `align-self`  
    30.5 `justify-content` And `align-content`  
    30.6 Overlapping Items  
31. **[Responsive Grids with `auto-fit`, `auto-fill` & `minmax()`](./[31]-Responsive-Grids.md)**  
    31.1 `minmax()`  
    31.2 `auto-fill` Vs. `auto-fit`  
    31.3 A Complete Responsive Card Grid  
    31.4 Combining With Media Queries  
    31.5 When To Use `minmax()` Without `repeat()`  
32. **[Grid vs. Flexbox: When to Use Which](./[32]-Grid-vs-Flexbox.md)**  
    32.1 The Core Difference  
    32.2 When Content Size Should Drive Layout: Flexbox  
    32.3 When Layout Should Drive Content Placement: Grid  
    32.4 Quick Decision Guide  
    32.5 Using Them Together  

**Responsive Design**  
    33. **[Responsive Design Principles & Mobile-First Approach](./[33]-Responsive-Design-Principles.md)**  
    34. **[Media Queries](./[34]-Media-Queries.md)**  
    35. **[Container Queries](./[35]-Container-Queries.md)**  
    36. **[Fluid Typography & Layout (`clamp()`, `min()`, `max()`)](./[36]-Fluid-Typography-and-Layout.md)**  
    37. **[Responsive Images & Media in CSS](./[37]-Responsive-Images-and-Media.md)**  

**Transitions, Animations & Transforms**  
    38. **[Transitions](./[38]-Transitions.md)**  
    39. **[2D & 3D Transforms (`translate`, `rotate`, `scale`, `skew`)](./[39]-Transforms.md)**  
    40. **[Keyframe Animations (`@keyframes`, `animation` property)](./[40]-Keyframe-Animations.md)**  
    41. **[Performance-Friendly Animation Practices](./[41]-Performance-Friendly-Animation.md)**  

**Variables & Modern CSS Features**  
    42. **[CSS Custom Properties (Variables)](./[42]-Custom-Properties-Variables.md)**  
    43. **[Functions (`calc()`, `min()`, `max()`, `clamp()`, `var()`)](./[43]-CSS-Functions.md)**  
    44. **[Logical Properties (`margin-inline`, `padding-block`, etc.)](./[44]-Logical-Properties.md)**  
    45. **[`@supports` & Feature Queries](./[45]-Supports-and-Feature-Queries.md)**  
    46. **[Nesting in CSS](./[46]-CSS-Nesting.md)**  

**Theming & Accessibility**  
    47. **[Dark Mode & `prefers-color-scheme`](./[47]-Dark-Mode-and-Prefers-Color-Scheme.md)**  
    48. **[Theming with Custom Properties](./[48]-Theming-with-Custom-Properties.md)**  
    49. **[Accessibility in CSS (focus styles, `prefers-reduced-motion`, contrast)](./[49]-Accessibility-in-CSS.md)**  
    50. **[Print Stylesheets (`@media print`)](./[50]-Print-Stylesheets.md)**  

**CSS Architecture & Methodologies**  
    51. **[Naming Methodologies (BEM, SMACSS, OOCSS)](./[51]-Naming-Methodologies.md)**  
    52. **[Utility-First CSS (Tailwind CSS approach)](./[52]-Utility-First-CSS.md)**  
    53. **[CSS Modules & Scoped Styles](./[53]-CSS-Modules.md)**  
    54. **[CSS-in-JS (styled-components, Emotion)](./[54]-CSS-in-JS.md)**  
    55. **[Organizing Large-Scale Stylesheets](./[55]-Organizing-Large-Scale-Stylesheets.md)**  

**Preprocessors & Tooling**  
    56. **[Sass/SCSS Fundamentals (variables, nesting, mixins, partials)](./[56]-Sass-SCSS-Fundamentals.md)**  
    57. **[Less Basics](./[57]-Less-Basics.md)**  
    58. **[PostCSS & Autoprefixer](./[58]-PostCSS-and-Autoprefixer.md)**  
    59. **[CSS Frameworks Overview (Bootstrap, Tailwind, Bulma)](./[59]-CSS-Frameworks-Overview.md)**  

**Performance & Best Practices**  
    60. **[CSS Performance & Rendering Optimization](./[60]-CSS-Performance-and-Rendering.md)**  
    61. **[Browser Compatibility & Vendor Prefixes](./[61]-Browser-Compatibility-and-Vendor-Prefixes.md)**  
    62. **[Validating & Linting CSS (Stylelint, W3C Validator)](./[62]-Validating-and-Linting-CSS.md)**  
    63. **[CSS Best Practices & Maintainable Style](./[63]-Best-Practices-and-Maintainable-Style.md)**