[⬅ Go to Frameworks](../[0]-Introduction-to-Frameworks.md) or [⬅ Go to JavaScript](/Programming/Syntax/JavaScript/[0]-Introduction-to-JavaScript.md)


<div align="center">
  <img src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/angular/angular-original.svg" width="50"/><br>
  <h1 style="margin-top: 0;">Angular</h1>
</div>


Angular is a full-featured, TypeScript-based frontend platform maintained by Google. Unlike lighter libraries, Angular ships with an opinionated set of built-in tools: a router, a forms system, an HTTP client, dependency injection, and a command-line interface, so teams get a consistent structure out of the box.

That structure makes Angular especially popular for large, long-lived enterprise applications. This Topic walks through TypeScript essentials, components and templates, signals, services and dependency injection, routing, forms, RxJS, testing, and deployment.

### Why Study Angular?

- **Everything is included** — routing, forms, HTTP, and testing tools come from one coherent, official source.
- **Strong conventions help big teams** — consistent structure makes large codebases easier to navigate and maintain.
- **Common in enterprise roles** — banks, insurers, and large organizations frequently build on Angular.
- **TypeScript by default** — you gain static typing habits that transfer to any modern frontend work.

### Angular At A Glance

| Attribute | Details |
|---|---|
| Language | TypeScript |
| Type | Frontend framework / platform |
| Created by | Google |
| Best for | Large enterprise single-page applications |
| Pairs well with | RxJS, NgRx, Angular Material, Nx |

---

### Table of Contents

**Basics**

   1. **[What Is Angular](./[1]-What-Is-Angular.md)**  
       1.1 Angular as an opinionated platform  
       1.2 Angular vs AngularJS  
       1.3 Installing the Angular CLI  
       1.4 Tour of a generated project  
   2. **[TypeScript For Angular](./[2]-TypeScript-For-Angular.md)**  
       2.1 Types and interfaces  
       2.2 Classes and access modifiers  
       2.3 Decorators  
       2.4 Generics and modules  
   3. **[Components And Templates](./[3]-Components-And-Templates.md)**  
       3.1 Anatomy of a component  
       3.2 Template syntax  
       3.3 Built-in control flow (@if, @for)  
       3.4 Component styles and encapsulation  

**Core Concepts**

   4. **[Data Binding And Directives](./[4]-Data-Binding-And-Directives.md)**  
       4.1 Interpolation and property binding  
       4.2 Event and two-way binding  
       4.3 Attribute and structural directives  
       4.4 Pipes  
   5. **[Component Communication](./[5]-Component-Communication.md)**  
       5.1 Inputs and outputs  
       5.2 Content projection  
       5.3 Template references and ViewChild  
       5.4 Parent and child patterns  
   6. **[Signals And Reactivity](./[6]-Signals-And-Reactivity.md)**  
       6.1 signal and computed  
       6.2 effect  
       6.3 Signal inputs and models  
       6.4 Signals vs RxJS  
   7. **[Services And Dependency Injection](./[7]-Services-And-Dependency-Injection.md)**  
       7.1 What a service is  
       7.2 The injector hierarchy  
       7.3 providedIn and providers  
       7.4 inject() function  
   8. **[Standalone Components And Modules](./[8]-Standalone-Components-And-Modules.md)**  
       8.1 Standalone components  
       8.2 NgModules and legacy code  
       8.3 Organizing features  
       8.4 Migration basics  
   9. **[Lifecycle Hooks](./[9]-Lifecycle-Hooks.md)**  
       9.1 ngOnInit and ngOnDestroy  
       9.2 ngOnChanges  
       9.3 View and content hooks  
       9.4 Cleanup patterns  

**Building Applications**

   10. **[Routing And Navigation](./[10]-Routing-And-Navigation.md)**  
       10.1 Configuring routes  
       10.2 Route and query parameters  
       10.3 Guards and resolvers  
       10.4 Lazy loading  
   11. **[Forms](./[11]-Forms.md)**  
       11.1 Template-driven forms  
       11.2 Reactive forms  
       11.3 Built-in and custom validators  
       11.4 Dynamic forms  
   12. **[HTTP And Interceptors](./[12]-HTTP-And-Interceptors.md)**  
       12.1 HttpClient basics  
       12.2 Typed requests and responses  
       12.3 Interceptors  
       12.4 Error handling and retries  
   13. **[RxJS And Observables](./[13]-RxJS-And-Observables.md)**  
       13.1 Observables explained  
       13.2 Common operators  
       13.3 Subjects  
       13.4 The async pipe and unsubscribing  
   14. **[State Management](./[14]-State-Management.md)**  
       14.1 Service-based state  
       14.2 NgRx basics  
       14.3 Signal-based stores  
       14.4 Choosing an approach  

**Quality And Delivery**

   15. **[Testing Angular Applications](./[15]-Testing-Angular-Applications.md)**  
       15.1 Jasmine, Karma, and Jest  
       15.2 TestBed and component tests  
       15.3 Testing services and HTTP  
       15.4 End-to-end testing  
   16. **[Performance And Change Detection](./[16]-Performance-And-Change-Detection.md)**  
       16.1 How change detection works  
       16.2 OnPush strategy  
       16.3 Deferrable views  
       16.4 SSR and hydration  
   17. **[UI With Angular Material](./[17]-UI-With-Angular-Material.md)**  
       17.1 Material components  
       17.2 The CDK  
       17.3 Theming  
       17.4 Accessibility and internationalization  
   18. **[Building And Deployment](./[18]-Building-And-Deployment.md)**  
       18.1 ng build and environments  
       18.2 Server-side rendering  
       18.3 Hosting options  
       18.4 CI/CD basics  
   19. **[Next Steps With Angular](./[19]-Next-Steps-With-Angular.md)**  
       19.1 Nx monorepos  
       19.2 Micro-frontends  
       19.3 Advanced RxJS  
       19.4 Keeping up with releases
