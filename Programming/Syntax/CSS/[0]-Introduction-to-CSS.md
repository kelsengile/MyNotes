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
    2. **[Adding CSS: Inline, Internal & External Stylesheets](./[2]-Adding-CSS.md)**  
    3. **[CSS Syntax: Rules, Selectors, Properties & Values](./[3]-CSS-Syntax.md)**  
    4. **[The Cascade, Specificity & Inheritance](./[4]-Cascade-Specificity-and-Inheritance.md)**  

**Selectors**  
    5. **[Basic Selectors (element, class, ID, universal)](./[5]-Basic-Selectors.md)**  
    6. **[Combinators (descendant, child, sibling)](./[6]-Combinators.md)**  
    7. **[Attribute Selectors](./[7]-Attribute-Selectors.md)**  
    8. **[Pseudo-Classes (`:hover`, `:nth-child`, `:focus`, `:not`, etc.)](./[8]-Pseudo-Classes.md)**  
    9. **[Pseudo-Elements (`::before`, `::after`, `::first-line`, etc.)](./[9]-Pseudo-Elements.md)**  

**The Box Model**  
    10. **[The Box Model: Content, Padding, Border & Margin](./[10]-The-Box-Model.md)**  
    11. **[Box Sizing (`box-sizing: border-box`)](./[11]-Box-Sizing.md)**  
    12. **[Margin Collapsing](./[12]-Margin-Collapsing.md)**  
    13. **[Display Property (block, inline, inline-block, none, contents)](./[13]-Display-Property.md)**  

**Colors, Units & Typography**  
    14. **[Color Formats (hex, rgb, hsl, named colors, alpha)](./[14]-Color-Formats.md)**  
    15. **[Units: Absolute vs. Relative (px, em, rem, %, vw, vh)](./[15]-Units.md)**  
    16. **[Typography: Fonts, Font Faces & Web Fonts (`@font-face`, Google Fonts)](./[16]-Typography-and-Web-Fonts.md)**  
    17. **[Text Styling (line-height, letter-spacing, text-align, text-decoration)](./[17]-Text-Styling.md)**  
    18. **[Backgrounds (color, image, gradient, position, size, repeat)](./[18]-Backgrounds.md)**  
    19. **[Borders, Shadows & Outlines (`box-shadow`, `border-radius`)](./[19]-Borders-Shadows-and-Outlines.md)**  

**Layout Fundamentals**  
    20. **[Normal Flow & Document Flow](./[20]-Normal-Flow.md)**  
    21. **[Positioning (static, relative, absolute, fixed, sticky)](./[21]-Positioning.md)**  
    22. **[Floats & Clearing (legacy layout)](./[22]-Floats-and-Clearing.md)**  
    23. **[Z-index & Stacking Contexts](./[23]-Z-index-and-Stacking-Contexts.md)**  

**Flexbox**  
    24. **[Flexbox Fundamentals (flex container & items)](./[24]-Flexbox-Fundamentals.md)**  
    25. **[Flex Direction, Wrap & Alignment](./[25]-Flex-Direction-Wrap-and-Alignment.md)**  
    26. **[Flex Grow, Shrink & Basis](./[26]-Flex-Grow-Shrink-and-Basis.md)**  
    27. **[Common Flexbox Layout Patterns](./[27]-Common-Flexbox-Patterns.md)**  

**CSS Grid**  
    28. **[Grid Fundamentals (grid container & items)](./[28]-Grid-Fundamentals.md)**  
    29. **[Defining Rows, Columns & Grid Template Areas](./[29]-Grid-Rows-Columns-and-Template-Areas.md)**  
    30. **[Grid Alignment & Placement (`justify-items`, `align-self`, `grid-column`)](./[30]-Grid-Alignment-and-Placement.md)**  
    31. **[Responsive Grids with `auto-fit`, `auto-fill` & `minmax()`](./[31]-Responsive-Grids.md)**  
    32. **[Grid vs. Flexbox: When to Use Which](./[32]-Grid-vs-Flexbox.md)**  

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