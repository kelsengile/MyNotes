[⬅ Go to Frameworks](../[0]-Introduction-to-Frameworks.md) or [⬅ Go to JavaScript](/Programming/Syntax/JavaScript/[0]-Introduction-to-JavaScript.md)


<div align="center">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/svelte/svelte-original.svg" width="50"/><br>
  <h1 style="margin-top: 0;">Svelte</h1>
</div>


Svelte is a frontend framework that works as a compiler. Instead of shipping a large runtime to the browser, Svelte turns your components into small, highly optimized vanilla JavaScript at build time, which means less code, fast startup, and no virtual DOM.

Its syntax stays close to plain HTML, CSS, and JavaScript, and its runes system makes reactivity explicit and simple. This Topic covers components, reactivity, bindings, shared state, transitions, and SvelteKit, the official application framework for routing, data loading, and deployment.

### Why Study Svelte?

- **Less boilerplate** — components read like enhanced HTML, so there is less ceremony to learn.
- **Small and fast output** — compiled code keeps bundles light and apps responsive.
- **Built-in features** — transitions, animations, and scoped styles need no extra libraries.
- **SvelteKit is a complete solution** — routing, server rendering, and deployment adapters come from the same team.

### Svelte At A Glance

| Attribute | Details |
|---|---|
| Language | JavaScript / TypeScript |
| Type | Compiler-based UI framework |
| Created by | Rich Harris (now maintained by the Svelte team) |
| Best for | Fast, lightweight apps and content sites |
| Pairs well with | SvelteKit, Vite, Tailwind CSS |

---

### Table of Contents

**Basics**

   1. **[What Is Svelte](./[1]-What-Is-Svelte.md)**  
       1.1 The compiler approach  
       1.2 Svelte vs virtual DOM frameworks  
       1.3 Creating a project  
       1.4 Anatomy of a .svelte file  
   2. **[Components And Markup](./[2]-Components-And-Markup.md)**  
       2.1 Expressions in markup  
       2.2 if, each, and await blocks  
       2.3 Snippets  
       2.4 Component styles scoped by default  
   3. **[Reactivity With Runes](./[3]-Reactivity-With-Runes.md)**  
       3.1 $state  
       3.2 $derived  
       3.3 $effect  
       3.4 When runes replace older patterns  

**Components In Depth**

   4. **[Props And Component Communication](./[4]-Props-And-Component-Communication.md)**  
       4.1 Declaring props with $props  
       4.2 Callback props  
       4.3 Children and snippets  
       4.4 Context  
   5. **[Bindings And Events](./[5]-Bindings-And-Events.md)**  
       5.1 bind:value and friends  
       5.2 Event handling  
       5.3 Forms  
       5.4 Actions with use:  
   6. **[Shared State And Stores](./[6]-Shared-State-And-Stores.md)**  
       6.1 Writable and readable stores  
       6.2 Derived stores  
       6.3 The $ store syntax  
       6.4 Shared reactive state with runes  
   7. **[Lifecycle And Effects](./[7]-Lifecycle-And-Effects.md)**  
       7.1 onMount and onDestroy  
       7.2 Effect cleanup  
       7.3 tick and timing  
       7.4 Working with the DOM  
   8. **[Transitions And Animations](./[8]-Transitions-And-Animations.md)**  
       8.1 Built-in transitions  
       8.2 Animations  
       8.3 Custom transitions  
       8.4 Motion with tweened and spring  

**SvelteKit**

   9. **[SvelteKit Routing](./[9]-SvelteKit-Routing.md)**  
       9.1 File-based routes  
       9.2 Layouts  
       9.3 Dynamic and optional parameters  
       9.4 Route groups  
   10. **[Loading Data](./[10]-Loading-Data.md)**  
       10.1 load functions  
       10.2 Server vs universal loading  
       10.3 Fetching and invalidation  
       10.4 Streaming data  
   11. **[Form Actions And Hooks](./[11]-Form-Actions-And-Hooks.md)**  
       11.1 Form actions  
       11.2 Progressive enhancement  
       11.3 Hooks and handle  
       11.4 Error handling  
   12. **[Rendering Modes And Adapters](./[12]-Rendering-Modes-And-Adapters.md)**  
       12.1 SSR, SSG, and prerendering  
       12.2 Client-only pages  
       12.3 Adapters  
       12.4 API endpoints  

**Quality And Delivery**

   13. **[TypeScript And Tooling](./[13]-TypeScript-And-Tooling.md)**  
       13.1 TypeScript in components  
       13.2 Typing props and load functions  
       13.3 Linting and formatting  
       13.4 Editor support  
   14. **[Testing Svelte Applications](./[14]-Testing-Svelte-Applications.md)**  
       14.1 Vitest and Testing Library  
       14.2 Component tests  
       14.3 End-to-end tests with Playwright  
       14.4 Mocking  
   15. **[Performance And Accessibility](./[15]-Performance-And-Accessibility.md)**  
       15.1 Compile-time warnings  
       15.2 Bundle size  
       15.3 Accessible components  
       15.4 Image and asset handling  
   16. **[Building And Deployment](./[16]-Building-And-Deployment.md)**  
       16.1 Production builds  
       16.2 Hosting choices  
       16.3 Environment variables  
       16.4 CI/CD basics  
   17. **[Next Steps With Svelte](./[17]-Next-Steps-With-Svelte.md)**  
       17.1 UI libraries  
       17.2 Svelte ecosystem tools  
       17.3 Migrating from Svelte 4  
       17.4 Project ideas
