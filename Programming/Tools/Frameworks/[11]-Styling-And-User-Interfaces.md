[Previous](./[10]-Routing-And-Navigation.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[12]-HTTP-And-REST-APIs.md)

*Building The Frontend*

# Lesson 11 - Styling And User Interfaces

A working interface is not enough: it also has to look good, adapt to different screens, and be usable by everyone. This lesson covers how frameworks approach styling, how ready-made component libraries speed up design, how to make layouts that work from phones to desktops, and the basics of accessibility.

## 11.1 CSS and Component Styling

On the web, appearance is controlled by **CSS (Cascading Style Sheets)**. A CSS rule selects elements and sets their visual properties:

```css
.button {
  background-color: #2563eb;
  color: white;
  padding: 0.5rem 1rem;
  border-radius: 0.25rem;
}
```

### The problem with global CSS

Traditional CSS is **global**: a rule like `.button` affects *every* element with that class, anywhere on the page. In a large app built from hundreds of components, this causes trouble:

- Two developers use the same class name for different things, and their styles collide.
- Changing a style in one place unexpectedly breaks another page.
- Nobody dares delete old CSS because they cannot tell what still uses it.

Component frameworks address this by tying styles to components so they stay isolated and can be removed together with the component.

### Common styling approaches

| Approach | How it works | Examples |
|---|---|---|
| **Plain / global CSS** | Ordinary stylesheets linked to the page | Any project; good for small sites |
| **CSS Modules** | Class names are automatically made unique per file | Common with React and Next.js |
| **Scoped styles** | Styles in a component apply only to that component | Vue.js (`<style scoped>`), Svelte (scoped by default), Angular (view encapsulation) |
| **CSS-in-JS** | Styles are written in JavaScript next to the component | styled-components, Emotion |
| **Utility-first CSS** | Small single-purpose classes combined in markup | Tailwind CSS |
| **Preprocessors** | Extended CSS syntax with variables and nesting, compiled to CSS | Sass |

### Utility-first CSS with Tailwind

**Tailwind CSS** takes a different approach from writing custom classes. It provides thousands of small utility classes, each doing one thing, that you apply directly in your markup:

```html
<button class="rounded bg-blue-600 px-4 py-2 text-white hover:bg-blue-700">
  Save
</button>
```

Reading the classes: rounded corners, a blue background, horizontal and vertical padding, white text, and a darker background on hover. There is no separate stylesheet to maintain, a design stays consistent because everyone draws from the same set of spacing and color values, and unused classes are removed in the production build. The trade-off is that markup can look crowded, and you need to learn the class names. Tailwind works alongside React, Next.js, Vue.js, Svelte, and others.

### Styling outside the browser

Mobile frameworks do not use CSS:

- **Flutter** styles widgets through their properties and a central `ThemeData` object that defines colors, fonts, and shapes for the whole app.
- **React Native** uses JavaScript style objects with CSS-like property names, created with `StyleSheet.create`, and positions elements using a flexbox-style layout.

### Design tokens and theming

Most projects define their colors, fonts, and spacing **once** (as CSS variables, Tailwind configuration, or a Flutter theme) and reuse them everywhere. These shared values are called **design tokens**. They keep the app consistent, and make changes such as a dark mode or a rebrand a matter of changing a few values.

---

## 11.2 UI Component Libraries

Building every button, dropdown, date picker, and dialog from scratch would take enormous time. A **UI component library** provides these pieces already designed, tested, and accessible.

| Ecosystem | Examples |
|---|---|
| React | Material UI, Chakra UI, Ant Design, Bootstrap-based libraries |
| Angular | Angular Material |
| Vue.js | Vuetify |
| Flutter | Material and Cupertino widgets (built into Flutter) |
| React Native | React Native Paper |
| Framework-independent | Bootstrap (CSS and optional JavaScript components) |

### Kinds of libraries

- **Styled libraries** come with a complete look, such as Google's Material Design. You get a polished app quickly, and customize through theming.
- **Headless libraries** provide the *behavior and accessibility* of components (keyboard handling, focus management, open/close logic) but **no visual style**, so you apply your own with CSS or Tailwind. Radix UI and Headless UI are examples.
- **Copy-in collections** place the component's source code directly into your project so you can freely edit it. shadcn/ui is a well-known example, built on headless components and Tailwind.

### A design system

Larger teams build a **design system**: a documented set of tokens, components, and usage rules that keeps every product they make consistent. A component library is often the code half of a design system.

### Trade-offs

| Benefit | Cost |
|---|---|
| Fast to build; consistent look | Apps can all look alike unless you customize |
| Built-in accessibility and tested behavior | Added download size |
| Less design work | Harder to bend to unusual designs |
| Large community and documentation | Dependency on the library's updates and direction |

A reasonable approach for most projects: use a component library for common elements, and customize only what makes your product distinct.

---

## 11.3 Responsive and Adaptive Design

People use your app on phones, tablets, laptops, and large monitors. **Responsive design** means a single layout that flexes to fit any screen size. **Adaptive design** means providing distinct layouts for particular screen categories or devices. Most modern apps are mainly responsive, with adaptive touches where it makes sense.

### The building blocks (web)

**1. The viewport tag.** Without it, mobile browsers pretend to be a wide desktop screen. Add this to the page's `<head>`:

```html
<meta name="viewport" content="width=device-width, initial-scale=1">
```

**2. Flexible units.** Prefer relative units over fixed pixel sizes.

| Unit | Relative to |
|---|---|
| `%` | The parent element's size |
| `rem` | The root font size (good for text and spacing) |
| `vw` / `vh` | A percentage of the viewport width or height |

**3. Flexbox and Grid.** Modern CSS layout systems adapt automatically. For example, this grid fits as many 250px-minimum columns as the screen allows:

```css
.cards {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 1rem;
}
```

**4. Media queries.** Apply different styles above or below certain screen widths, called **breakpoints**:

```css
.sidebar { display: none; }            /* hidden on small screens */

@media (min-width: 768px) {
  .sidebar { display: block; }         /* shown on screens 768px or wider */
}
```

### Mobile-first

A widely recommended habit is **mobile-first**: write styles for the smallest screen first, then use `min-width` media queries to add enhancements for larger screens. This keeps the base design simple and fast on phones.

Tailwind builds this into its class names with breakpoint prefixes, where unprefixed classes apply everywhere and prefixed ones apply from that size up:

```html
<div class="flex flex-col md:flex-row">
  <!-- stacked on phones, side-by-side from the medium breakpoint upward -->
</div>
```

### Responsive and adaptive design in mobile frameworks

Flutter and React Native apps also run on many screen sizes (phones, tablets, foldables, and, for Flutter, web and desktop). Flutter offers `MediaQuery` and `LayoutBuilder` to read the available space and choose a layout accordingly, and React Native provides tools to read screen dimensions and orientation. A typical adaptive pattern is a single column of content on a phone and a list-plus-details side-by-side view on a tablet.

### Tips

- Test at several sizes, using the browser's device-simulation mode and, ideally, real devices.
- Make tap targets large enough to press comfortably with a finger.
- Do not assume a mouse: hover effects must not be the *only* way to reveal something.
- Let text wrap instead of cutting it off.

---

## 11.4 Accessibility Basics

**Accessibility** (often shortened to **a11y**) means making your app usable by people with a wide range of abilities and circumstances: people who are blind or have low vision, who are deaf or hard of hearing, who cannot use a mouse, who have motor or cognitive disabilities, or who are simply using a phone in bright sunlight. It is also, in many places, a legal requirement, and it improves the experience for everyone.

The standard reference is the **Web Content Accessibility Guidelines (WCAG)**, published by the W3C (see the W3C Web Accessibility Initiative at w3.org/WAI). Their principles can be remembered as: content must be **perceivable**, **operable**, **understandable**, and **robust**.

### The essentials

**1. Use semantic HTML.** Choose elements for what they *mean*, not how they look. A real `<button>` is keyboard-focusable, announced as a button by screen readers, and responds to Enter and Space automatically. A `<div>` styled to look like a button does none of that.

```html
<!-- Good -->
<button type="submit">Send</button>

<!-- Poor: looks similar but is not accessible by default -->
<div class="button" onclick="send()">Send</div>
```

Use headings (`<h1>` to `<h6>`) in a logical order, lists for lists, and landmarks like `<nav>`, `<main>`, and `<footer>` to describe page structure.

**2. Provide text alternatives.** Images need an `alt` description so screen-reader users know what they show. Purely decorative images should have an empty `alt=""` so they are skipped.

```html
<img src="chart.png" alt="Bar chart showing sales rising each month">
```

**3. Label form fields.** Every input needs a visible, programmatically connected label:

```html
<label for="email">Email address</label>
<input id="email" type="email">
```

**4. Support the keyboard.** Everything that can be done with a mouse must be possible with the keyboard alone, in a sensible tab order, and the currently focused element must have a **visible focus indicator**. Never remove focus outlines without replacing them with something clear.

**5. Ensure enough color contrast.** Text must stand out from its background. Under WCAG level AA, normal-sized text needs a contrast ratio of at least 4.5:1. Also, never use color as the *only* way to convey information (for example, show an error with an icon or text as well as red).

**6. Use ARIA sparingly.** ARIA attributes (such as `aria-label`) add extra meaning for assistive technology when no suitable native HTML element exists. The common guidance is: **use native HTML first, and ARIA only to fill gaps.** Incorrect ARIA can make things worse than none.

**7. Respect user preferences.** Support zoom and larger text, and honor settings such as "reduce motion" for animations.

### Accessibility in mobile frameworks

Mobile platforms have their own screen readers (VoiceOver on iOS, TalkBack on Android), and frameworks expose ways to describe your UI to them: the **Semantics** widget in Flutter, and props such as `accessibilityLabel` and `accessibilityRole` in React Native.

### Testing for accessibility

- Try your app using **only the keyboard**.
- Turn on a **screen reader** and listen to how the page is announced.
- Use automated checkers such as browser audits (Lighthouse) and the axe tools. They catch many, but not all, problems, so manual checks still matter.

Good component libraries (11.2) handle many of these details for you, which is one more reason to use them for common controls.

---

[Previous](./[10]-Routing-And-Navigation.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[12]-HTTP-And-REST-APIs.md)
