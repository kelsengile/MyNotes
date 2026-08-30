[⬅ Back to README](../../../README.md)

# Web Development

Welcome! This is a self-paced course for learning Web Development, the practice of building websites and web applications that run in a browser and, often, talk to a server behind the scenes.

---

## What is Web Development?

Web Development lets you:
- Build pages with HTML, style them with CSS, and bring them to life with JavaScript
- Design layouts that adapt to any screen size, from phones to desktops
- Manipulate the DOM and respond to user interaction in real time
- Fetch and display data from external APIs, and push data in real time with WebSockets
- Organize front-end code with modern tooling, bundlers, and package managers
- Catch bugs early with typed JavaScript and automated tests
- Build and consume your own REST APIs
- Work with servers, databases, and authentication
- Deploy a project so anyone in the world can visit it, with automated pipelines behind it
- Write code that's accessible, performant, installable, and secure by default

> **Note:** HTML, CSS, and JavaScript fundamentals are covered in their own dedicated folders. This course picks up from there and covers everything else needed to build and deploy a real website.

## Table of Contents

**Getting Started**  
    1. **[How the Web Works (Client-Server, HTTP, Browsers)](./[1]-How-The-Web-Works.md)**  
       1.1 Clients and Servers  
       1.2 URLs and DNS  
       1.3 HTTP and HTTPS  
       1.4 Rendering a Page  
       1.5 Static vs Dynamic Content  
    2. **[Your Development Environment (editor, dev tools, live server)](./[2]-Development-Environment.md)**  
       2.1 Choosing a Code Editor  
       2.2 Browser Developer Tools  
       2.3 Running a Local Server  
       2.4 The Terminal  
       2.5 Browser Extensions and Testing Tools  
    3. **[Anatomy of a Web Project (files, folders, entry points)](./[3]-Anatomy-of-a-Web-Project.md)**  
       3.1 A Minimal Project Structure  
       3.2 Growing Beyond a Single Page  
       3.3 Configuration Files  
       3.4 The Entry Point and the Build Output  

**Intermediate JavaScript**  
    4. **[Asynchronous JavaScript (Callbacks, Promises, Async/Await)](./[4]-Asynchronous-JavaScript.md)**  
       4.1 Why Asynchronous Code Exists  
       4.2 The Event Loop, Briefly  
       4.3 Callbacks  
       4.4 Promises  
       4.5 Async/Await  
       4.6 Running Things in Parallel  
    5. **[Fetching Data with the Fetch API](./[5]-Fetch-API.md)**  
       5.1 Making a Basic Request  
       5.2 Checking for Errors  
       5.3 Sending Data with POST  
       5.4 Handling Failures Gracefully  
       5.5 CORS in Brief  
    6. **[ES Modules & Code Organization](./[6]-ES-Modules.md)**  
       6.1 Why Split Code into Modules  
       6.2 Exporting and Importing  
       6.3 Default Exports  
       6.4 Using Modules in the Browser  
       6.5 CommonJS, for Context  
    7. **[Error Handling & Debugging in the Browser](./[7]-Error-Handling-and-Debugging.md)**  
       7.1 Reading Errors in the Console  
       7.2 try/catch/finally  
       7.3 Throwing and Custom Errors  
       7.4 Debugging with Breakpoints  
       7.5 Handling Errors in Async Code  
    8. **[TypeScript Basics](./[8]-TypeScript-Basics.md)**  
       8.1 What TypeScript Adds  
       8.2 Basic Type Annotations  
       8.3 Interfaces and Object Shapes  
       8.4 Type Inference  
       8.5 Union Types and Generics, Briefly  

**Styling at Scale**  
    9. **[CSS Preprocessors (Sass/SCSS)](./[9]-CSS-Preprocessors.md)**  
       9.1 Why Preprocessors Exist  
       9.2 Variables  
       9.3 Nesting  
       9.4 Partials and Imports  
       9.5 Mixins  
    10. **[CSS Architecture & Naming Conventions (BEM)](./[10]-CSS-Architecture-and-BEM.md)**  
        10.1 The Problem: CSS Doesn't Scale on Its Own  
        10.2 BEM: Block, Element, Modifier  
        10.3 Flat Specificity by Design  
        10.4 Component-Scoped Styles as an Alternative  
        10.5 Utility-First as a Different Philosophy  
    11. **[CSS Frameworks (Bootstrap, Tailwind)](./[11]-CSS-Frameworks.md)**  
        11.1 Why Use a Framework  
        11.2 Component-Based Frameworks: Bootstrap  
        11.3 Utility-First Frameworks: Tailwind CSS  
        11.4 Build-Time Optimization  
        11.5 Choosing an Approach  

**Tooling & Build Systems**  
    12. **[Package Managers (npm, yarn)](./[12]-Package-Managers.md)**  
        12.1 What a Package Manager Does  
        12.2 package.json  
        12.3 Installing Packages  
        12.4 Semantic Versioning and Lockfiles  
        12.5 Running Scripts  
    13. **[Module Bundlers (Vite, Webpack)](./[13]-Module-Bundlers.md)**  
        13.1 The Problem Bundlers Solve  
        13.2 What Bundling Actually Does  
        13.3 Vite  
        13.4 Webpack  
        13.5 Hot Module Replacement  
    14. **[Version Control with Git & GitHub for Web Projects](./[14]-Version-Control-with-Git.md)**  
        14.1 What Git Is  
        14.2 The Core Workflow  
        14.3 Branches  
        14.4 GitHub and Remotes  
        14.5 Pull Requests and Merging  
        14.6 .gitignore  
    15. **[Linting & Code Formatting (ESLint, Prettier)](./[15]-Linting-and-Formatting.md)**  
        15.1 Linting vs Formatting  
        15.2 ESLint  
        15.3 Prettier  
        15.4 Using Them Together  
        15.5 Enforcing Standards Automatically  
    16. **[Testing JavaScript (Unit & End-to-End Testing)](./[16]-Testing-JavaScript.md)**  
        16.1 Why Automated Tests Matter  
        16.2 Unit Tests  
        16.3 Integration Tests  
        16.4 End-to-End (E2E) Tests  
        16.5 The Testing Pyramid  

**Front-End Frameworks**  
    17. **[Introduction to Component-Based UI](./[17]-Component-Based-UI.md)**  
        17.1 The Core Idea  
        17.2 Why Components, Not Just Functions  
        17.3 Declarative vs Imperative UI  
        17.4 The Virtual DOM, Briefly  
        17.5 Choosing a Framework  
    18. **[React Fundamentals (Components, Props, State)](./[18]-React-Fundamentals.md)**  
        18.1 Your First Component  
        18.2 Props  
        18.3 State with useState  
        18.4 Handling Events  
        18.5 useEffect and Side Effects  
        18.6 Composing Components  
    19. **[Client-Side Routing](./[19]-Client-Side-Routing.md)**  
        19.1 Multi-Page Sites vs Single-Page Apps  
        19.2 The History API  
        19.3 Routing in React  
        19.4 Dynamic Routes and Parameters  
        19.5 The Server-Side Gap  
    20. **[State Management (Context, Redux)](./[20]-State-Management.md)**  
        20.1 The "Prop Drilling" Problem  
        20.2 React Context  
        20.3 When You Need More: External State Libraries  
        20.4 Lighter Alternatives  
        20.5 Server State vs Client State  

**Back-End Basics**  
    21. **[Introduction to Servers & Node.js](./[21]-Servers-and-Node.md)**  
        21.1 What a Server Actually Is  
        21.2 Node.js  
        21.3 Ports  
        21.4 Express: A Minimal Web Framework  
        21.5 Middleware  
    22. **[Building a Simple REST API](./[22]-Building-a-REST-API.md)**  
        22.1 What REST Means  
        22.2 Resources and HTTP Methods  
        22.3 A Minimal API with Express  
        22.4 Status Codes That Matter  
        22.5 Validating Input  
    23. **[Working with Databases (SQL vs NoSQL basics)](./[23]-Working-with-Databases.md)**  
        23.1 Why Not Just Use Variables?  
        23.2 SQL (Relational) Databases  
        23.3 NoSQL (Document) Databases  
        23.4 Choosing Between Them  
        23.5 Connecting from Node.js  
    24. **[Real-Time Communication with WebSockets](./[24]-WebSockets-and-Realtime-Communication.md)**  
        24.1 The Limits of Request/Response  
        24.2 What WebSockets Provide  
        24.3 A Minimal WebSocket Server  
        24.4 When to Use WebSockets vs Polling  
        24.5 Real-World Considerations  

**Full-Stack Concepts**  
    25. **[Authentication & Sessions](./[25]-Authentication-and-Sessions.md)**  
        25.1 Authentication vs Authorization  
        25.2 Passwords Must Be Hashed  
        25.3 HTTP Is Stateless — Sessions Fix That  
        25.4 Token-Based Auth: JWTs  
        25.5 Protecting Routes  
    26. **[Connecting Front-End to Back-End (API Integration)](./[26]-Connecting-Frontend-to-Backend.md)**  
        26.1 Putting the Pieces Together  
        26.2 A Typical Data-Fetching Component  
        26.3 Sending Authenticated Requests  
        26.4 The Same-Origin vs Cross-Origin Question  
        26.5 Keeping UI and Server Data in Sync  
    27. **[Environment Variables & Configuration](./[27]-Environment-Variables.md)**  
        27.1 The Problem: Config That Shouldn't Be in Code  
        27.2 What Environment Variables Are  
        27.3 .env Files for Local Development  
        27.4 Providing Variables in Production  
        27.5 Front-End Environment Variables Are Not Secret  

**Deployment & Production**  
    28. **[Hosting & Deploying a Website](./[28]-Hosting-and-Deploying.md)**  
        28.1 What "Deploying" Means  
        28.2 Static Hosting  
        28.3 Hosting a Backend Server  
        28.4 Containers, Briefly  
        28.5 The Build-and-Deploy Sequence  
    29. **[Domains, DNS & HTTPS](./[29]-Domains-DNS-and-HTTPS.md)**  
        29.1 Buying and Owning a Domain  
        29.2 DNS Records  
        29.3 Connecting a Domain to a Host  
        29.4 HTTPS and Certificates  
        29.5 Subdomains  
    30. **[CI/CD for Web Projects](./[30]-CI-CD-for-Web-Projects.md)**  
        30.1 Continuous Integration (CI)  
        30.2 Continuous Deployment/Delivery (CD)  
        30.3 A Basic GitHub Actions Workflow  
        30.4 Deploying from CI  
        30.5 Secrets in CI  
    31. **[Performance Optimization Basics](./[31]-Performance-Optimization.md)**  
        31.1 Why Performance Matters  
        31.2 Core Web Vitals  
        31.3 Reducing What Gets Sent  
        31.4 Lazy Loading  
        31.5 Caching  
    32. **[Progressive Web Apps](./[32]-Progressive-Web-Apps.md)**  
        32.1 What a PWA Is  
        32.2 The Web App Manifest  
        32.3 Service Workers  
        32.4 Installability  
        32.5 When a PWA Makes Sense  

**Best Practices**  
    33. **[Web Accessibility (a11y)](./[33]-Web-Accessibility.md)**  
        33.1 What Accessibility Means  
        33.2 Semantic HTML First  
        33.3 ARIA, Used Sparingly  
        33.4 Keyboard Navigation  
        33.5 Alt Text and Color Contrast  
        33.6 Testing Accessibility  
    34. **[SEO Fundamentals](./[34]-SEO-Fundamentals.md)**  
        34.1 What SEO Is  
        34.2 Title Tags and Meta Descriptions  
        34.3 Semantic Structure Helps Search Engines Too  
        34.4 Crawlability  
        34.5 Performance and Mobile-Friendliness  
        34.6 Structured Data  
    35. **[Browser Compatibility & Progressive Enhancement](./[35]-Browser-Compatibility.md)**  
        35.1 Why Browsers Behave Differently  
        35.2 Checking Compatibility  
        35.3 Progressive Enhancement  
        35.4 Polyfills and Transpilation  
        35.5 Deciding What to Support  
    36. **[Security Basics for Web Developers (CORS, CSRF, XSS)](./[36]-Security-Basics.md)**
        36.1 CORS Revisited  
        36.2 XSS (Cross-Site Scripting)  
        36.3 CSRF (Cross-Site Request Forgery)  
        36.4 SQL Injection  
        36.5 A Few General Principles  