[⬅ Go to Frameworks](../[0]-Introduction-to-Frameworks.md) or [⬅ Go to CSS](/Programming/Syntax/CSS/[0]-Introduction-to-CSS.md)


<div align="center">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/tailwindcss/tailwindcss-original.svg" width="50"/><br>
  <h1 style="margin-top: 0;">Tailwind CSS</h1>
</div>


Tailwind CSS is a utility-first CSS framework. Instead of writing custom stylesheets with names like .card or .button, you compose designs directly in your markup using small, single-purpose classes such as flex, p-4, and text-center.

Tailwind scans your files and generates only the CSS you actually use, which keeps output small and design consistent. This Topic covers utility fundamentals, layout, typography, responsive design, state variants, dark mode, theme customization, reusing styles, and working with frameworks and component libraries.

### Why Study Tailwind CSS?

- **Build interfaces quickly** — style as you write markup, without switching between files.
- **Consistent design by default** — a shared spacing, color, and type scale keeps UIs coherent.
- **Small production CSS** — only the classes you use are included in the final build.
- **Works with every frontend tool** — a natural fit for React, Next.js, Vue, Svelte, and plain HTML.

### Tailwind CSS At A Glance

| Attribute | Details |
|---|---|
| Language | CSS (utility classes in HTML) |
| Type | Utility-first CSS framework |
| Created by | Adam Wathan and Tailwind Labs |
| Best for | Custom designs built quickly and consistently |
| Pairs well with | React, Next.js, Vue, Svelte, Vite |

---

### Table of Contents

**Basics**

   1. **[What Is Tailwind CSS](./[1]-What-Is-Tailwind-CSS.md)**  
       1.1 Utility-first explained  
       1.2 Tailwind vs Bootstrap and plain CSS  
       1.3 Installing with Vite or the CLI  
       1.4 How Tailwind generates CSS  
   2. **[Utility-First Fundamentals](./[2]-Utility-First-Fundamentals.md)**  
       2.1 Composing classes  
       2.2 Readability trade-offs  
       2.3 Arbitrary values  
       2.4 Important modifier and conflicts  
   3. **[Layout](./[3]-Layout.md)**  
       3.1 Display and containers  
       3.2 Flexbox utilities  
       3.3 Grid utilities  
       3.4 Positioning and z-index  
   4. **[Spacing, Sizing, And Borders](./[4]-Spacing-Sizing-And-Borders.md)**  
       4.1 Padding and margin  
       4.2 Width and height  
       4.3 Borders and radius  
       4.4 Divide and ring utilities  
   5. **[Typography And Colors](./[5]-Typography-And-Colors.md)**  
       5.1 Font size, weight, and family  
       5.2 Text alignment and spacing  
       5.3 The color palette  
       5.4 Opacity and gradients  
   6. **[Backgrounds And Effects](./[6]-Backgrounds-And-Effects.md)**  
       6.1 Background utilities  
       6.2 Shadows  
       6.3 Filters and blur  
       6.4 Images and object-fit  

**Responsive And Interactive Design**

   7. **[State Variants](./[7]-State-Variants.md)**  
       7.1 Hover and focus  
       7.2 Active and disabled  
       7.3 Group and peer  
       7.4 Pseudo-elements  
   8. **[Responsive Design](./[8]-Responsive-Design.md)**  
       8.1 Mobile-first breakpoints  
       8.2 Responsive utility prefixes  
       8.3 Container queries  
       8.4 Custom breakpoints  
   9. **[Dark Mode](./[9]-Dark-Mode.md)**  
       9.1 The dark variant  
       9.2 System preference vs manual toggle  
       9.3 Designing for both themes  
       9.4 Color tokens  
   10. **[Animations And Transitions](./[10]-Animations-And-Transitions.md)**  
       10.1 Transition utilities  
       10.2 Transforms  
       10.3 Built-in animations  
       10.4 Custom keyframes  

**Customization**

   11. **[Theme Customization](./[11]-Theme-Customization.md)**  
       11.1 Design tokens and the theme  
       11.2 Custom colors and fonts  
       11.3 Spacing and breakpoints  
       11.4 Extending vs overriding  
   12. **[Reusing Styles](./[12]-Reusing-Styles.md)**  
       12.1 Components over classes  
       12.2 Extracting with @apply  
       12.3 Layers and custom CSS  
       12.4 Avoiding duplication  
   13. **[Plugins And Typography](./[13]-Plugins-And-Typography.md)**  
       13.1 Official plugins  
       13.2 The typography plugin  
       13.3 Forms plugin  
       13.4 Writing a simple plugin  

**Practical Use**

   14. **[Using Tailwind With Frameworks](./[14]-Using-Tailwind-With-Frameworks.md)**  
       14.1 Tailwind with React and Next.js  
       14.2 Tailwind with Vue and Svelte  
       14.3 Class composition helpers  
       14.4 Conditional classes  
   15. **[Component Libraries](./[15]-Component-Libraries.md)**  
       15.1 shadcn/ui  
       15.2 Headless UI  
       15.3 daisyUI and Flowbite  
       15.4 Choosing a library  
   16. **[Accessibility And Best Practices](./[16]-Accessibility-And-Best-Practices.md)**  
       16.1 Semantic HTML first  
       16.2 Focus styles and contrast  
       16.3 Screen reader utilities  
       16.4 Maintainable class organization  
   17. **[Performance And Production](./[17]-Performance-And-Production.md)**  
       17.1 How unused CSS is removed  
       17.2 Minification and build size  
       17.3 Prettier class sorting  
       17.4 Editor tooling  
   18. **[Next Steps With Tailwind CSS](./[18]-Next-Steps-With-Tailwind-CSS.md)**  
       18.1 Design systems  
       18.2 Advanced layouts  
       18.3 Animation libraries  
       18.4 Project ideas
