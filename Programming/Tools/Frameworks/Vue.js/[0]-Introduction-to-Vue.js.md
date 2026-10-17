[⬅ Go to Frameworks](../[0]-Introduction-to-Frameworks.md) or [⬅ Go to JavaScript](/Programming/Syntax/JavaScript/[0]-Introduction-to-JavaScript.md)


<div align="center">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/vuejs/vuejs-original.svg" width="50"/><br>
  <h1 style="margin-top: 0;">Vue.js</h1>
</div>


Vue.js is a progressive JavaScript framework for building user interfaces. It can be dropped into a single HTML page to enhance a small widget, or scaled up into a full single-page application with routing and state management, which is why it is known for its gentle learning curve.

Vue's single-file components keep a component's template, logic, and styles together in one place, and its reactivity system tracks changes automatically. This Topic covers templates, reactivity, the Composition API, components, Vue Router, Pinia, and the surrounding ecosystem including Nuxt.

### Why Study Vue.js?

- **Easy to start, able to scale** — you can adopt Vue one piece at a time and grow into a full application.
- **Clear, readable single-file components** — template, script, and style live side by side.
- **Excellent official tooling** — Vue Router, Pinia, and Vite are maintained alongside the core framework.
- **Popular worldwide** — widely used in startups, agencies, and large companies, especially across Europe and Asia.

### Vue.js At A Glance

| Attribute | Details |
|---|---|
| Language | JavaScript / TypeScript |
| Type | Frontend framework |
| Created by | Evan You |
| Best for | Approachable apps from small widgets to full SPAs |
| Pairs well with | Vite, Pinia, Vue Router, Nuxt |

---

### Table of Contents

**Basics**

   1. **[What Is Vue](./[1]-What-Is-Vue.md)**  
       1.1 The progressive framework idea  
       1.2 Options API vs Composition API  
       1.3 Creating a project with create-vue  
       1.4 Anatomy of a single-file component  
   2. **[Template Syntax](./[2]-Template-Syntax.md)**  
       2.1 Interpolation  
       2.2 Directives like v-bind and v-on  
       2.3 Conditional rendering with v-if  
       2.4 List rendering with v-for  
   3. **[Reactivity Fundamentals](./[3]-Reactivity-Fundamentals.md)**  
       3.1 ref and reactive  
       3.2 Computed properties  
       3.3 Watchers  
       3.4 How Vue tracks changes  

**Components**

   4. **[Components And Props](./[4]-Components-And-Props.md)**  
       4.1 Defining and registering components  
       4.2 Passing props  
       4.3 Prop validation  
       4.4 One-way data flow  
   5. **[Events And Slots](./[5]-Events-And-Slots.md)**  
       5.1 Emitting events  
       5.2 v-model on components  
       5.3 Slots and scoped slots  
       5.4 Provide and inject  
   6. **[Composition API And Composables](./[6]-Composition-API-And-Composables.md)**  
       6.1 script setup  
       6.2 Extracting logic into composables  
       6.3 Reusing stateful logic  
       6.4 Naming and organization  
   7. **[Lifecycle Hooks](./[7]-Lifecycle-Hooks.md)**  
       7.1 onMounted and onUnmounted  
       7.2 Update hooks  
       7.3 Cleanup patterns  
       7.4 Template refs  
   8. **[Forms And User Input](./[8]-Forms-And-User-Input.md)**  
       8.1 v-model on inputs  
       8.2 Modifiers like .lazy and .trim  
       8.3 Validation approaches  
       8.4 Form libraries  
   9. **[Transitions And Directives](./[9]-Transitions-And-Directives.md)**  
       9.1 Transition and TransitionGroup  
       9.2 Custom directives  
       9.3 Plugins  
       9.4 Teleport and Suspense  

**Building Applications**

   10. **[Vue Router](./[10]-Vue-Router.md)**  
       10.1 Configuring routes  
       10.2 Dynamic and nested routes  
       10.3 Navigation guards  
       10.4 Lazy-loaded routes  
   11. **[State Management With Pinia](./[11]-State-Management-With-Pinia.md)**  
       11.1 Why shared state is needed  
       11.2 Defining stores  
       11.3 Actions and getters  
       11.4 Persisting state  
   12. **[Data Fetching](./[12]-Data-Fetching.md)**  
       12.1 Fetching with fetch and Axios  
       12.2 Loading and error states  
       12.3 Composable-based fetching  
       12.4 Caching with Vue Query  
   13. **[Styling Vue Components](./[13]-Styling-Vue-Components.md)**  
       13.1 Scoped styles  
       13.2 CSS Modules  
       13.3 Using Tailwind CSS  
       13.4 Dynamic styling with v-bind  
   14. **[TypeScript With Vue](./[14]-TypeScript-With-Vue.md)**  
       14.1 Typing props and emits  
       14.2 Typing refs and stores  
       14.3 Generic components  
       14.4 Volar and tooling  

**Ecosystem And Delivery**

   15. **[Nuxt And Server-Side Rendering](./[15]-Nuxt-And-Server-Side-Rendering.md)**  
       15.1 What Nuxt adds  
       15.2 File-based routing  
       15.3 Rendering modes  
       15.4 Server routes  
   16. **[Testing Vue Applications](./[16]-Testing-Vue-Applications.md)**  
       16.1 Vitest and Vue Test Utils  
       16.2 Component testing  
       16.3 Mocking stores and routers  
       16.4 End-to-end tests  
   17. **[Performance Optimization](./[17]-Performance-Optimization.md)**  
       17.1 Reactivity pitfalls  
       17.2 Lazy loading components  
       17.3 Virtual scrolling  
       17.4 Build analysis  
   18. **[Building And Deployment](./[18]-Building-And-Deployment.md)**  
       18.1 Vite production builds  
       18.2 Environment variables  
       18.3 Hosting options  
       18.4 CI/CD basics  
   19. **[Next Steps With Vue](./[19]-Next-Steps-With-Vue.md)**  
       19.1 UI libraries like Vuetify and PrimeVue  
       19.2 Advanced patterns  
       19.3 Vue 3 internals  
       19.4 Project ideas
