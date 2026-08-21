[Previous](./[10]-CSS-Architecture-and-BEM.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[12]-Package-Managers.md)

*Styling at Scale*

# Lesson 11 - CSS Frameworks (Bootstrap, Tailwind)

## 11.1 Why Use a Framework

Writing every layout, button style, and grid system from scratch is slow and error-prone. **CSS frameworks** ship pre-built, tested styles and components so teams can move faster and stay visually consistent, at the cost of some flexibility and, often, extra file size unless configured carefully.

---

## 11.2 Component-Based Frameworks: Bootstrap

**Bootstrap** provides ready-made components — navbars, buttons, modals, grids — styled out of the box and controlled through class names:

```html
<button class="btn btn-primary">Save</button>
<div class="container">
  <div class="row">
    <div class="col-md-6">Left</div>
    <div class="col-md-6">Right</div>
  </div>
</div>
```

This gets a professional-looking UI running quickly, but customizing it deeply often means fighting the framework's defaults or overriding its CSS.

---

## 11.3 Utility-First Frameworks: Tailwind CSS

**Tailwind CSS** takes a different approach: instead of pre-built components, it provides small utility classes, each doing one job, composed directly in your markup:

```html
<button class="bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700">
  Save
</button>
```

There's no `.btn-primary` to override — you build the exact look you want from primitives. This avoids fighting framework opinions but means your HTML carries more classes, and unstyled, semantic components have to be built yourself (or imported from a companion component library).

---

## 11.4 Build-Time Optimization

Both approaches now typically run through a build step (Lesson 13). Tailwind, in particular, scans your source files and generates only the CSS for classes you actually used, keeping shipped file size small despite offering thousands of possible utility combinations.

---

## 11.5 Choosing an Approach

Component frameworks like Bootstrap suit projects that need to look polished fast with minimal custom design. Utility-first frameworks like Tailwind suit projects with a specific design system, or teams that prefer configuring look-and-feel directly in markup rather than maintaining separate CSS files. Neither is universally "better" — the right choice depends on the project's design requirements and the team's workflow preferences.

---

[Previous](./[10]-CSS-Architecture-and-BEM.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[12]-Package-Managers.md)
