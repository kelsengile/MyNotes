[Previous](./[9]-State-Management.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[11]-Styling-And-User-Interfaces.md)

*Building The Frontend*

# Lesson 10 - Routing And Navigation

**Routing** is how an application decides what to show or run for a given address or screen. It is one of the first things every framework provides, and the concept appears in both web and mobile development, with different flavors. This lesson covers where routing happens (browser or server), how routes are defined, how to handle dynamic and nested routes, and how navigation works in mobile apps.

## 10.1 Client-Side vs Server-Side Routing

### Server-side routing

In **server-side routing**, every URL the user visits is a request sent to a server. The server matches the URL to a route, builds a response (usually a complete HTML page), and sends it back. Clicking a link loads a new page from the server.

```text
Browser ── GET /about ──► Server finds route "/about" ──► returns full HTML page
```

This is how traditional websites and server-rendered frameworks such as Django, Laravel, and Rails work. Pages are simple, work without JavaScript, and are easy for search engines to read. The cost is that each navigation reloads the whole page.

### Client-side routing

In **client-side routing**, the browser loads the application once. After that, clicking a link does **not** request a new page. Instead, JavaScript:

1. Intercepts the click.
2. Updates the address bar using the browser's **History API**, without reloading.
3. Swaps the on-screen components to show the new "page".

```text
Browser (app already loaded) ── click "About" ──► JS updates URL to /about
                                                  and shows the About component
```

This makes navigation feel instant and app-like, and is how **single-page applications (SPAs)** built with React, Angular, Vue.js, and Svelte typically work. The data for each view is usually fetched from an API.

### Comparing the two

| | Server-side routing | Client-side routing |
|---|---|---|
| **Where URL matching happens** | On the server | In the browser |
| **Navigation** | New page request each time | Instant swap without reload |
| **First load** | Usually fast: HTML arrives ready | Can be slower: the app's JavaScript must load first |
| **Search engines** | Easy | Needs extra care (see rendering strategies, Lesson 18) |
| **Feels like** | A website | An app |

### The hybrid approach

Meta-frameworks such as Next.js combine both: the **first** request is handled by the server (so the first page arrives as ready-made HTML), and **later** navigation is handled in the browser. You get fast first loads and smooth navigation.

> **Important:** even in an SPA, the server must be set up to return the app for any URL, otherwise reloading the page at `/about` would produce a 404. Frameworks and hosting platforms provide a standard "fallback to index" setting for this.

---

## 10.2 Defining Routes

A **route** links a **path** (and, for servers, an HTTP method) to some code that should run or a component that should be shown. A collection of routes is a **route table**. Frameworks offer three main ways to define it.

### Style 1: Explicit route registration

You register each route in code. This is how most backend frameworks work.

**Express (JavaScript)**

```javascript
const express = require("express");
const app = express();

app.get("/", (req, res) => res.send("Home"));
app.get("/about", (req, res) => res.send("About us"));

// :id is a dynamic segment; ?tab=... is a query string
app.get("/users/:id", (req, res) => {
  res.send(`User ${req.params.id}, tab: ${req.query.tab || "profile"}`);
});

app.listen(3000);
```

Running it and requesting each address gives:

```text
GET /                 →  Home
GET /about            →  About us
GET /users/42?tab=posts  →  User 42, tab: posts
GET /users/7          →  User 7, tab: profile
```

**Flask (Python)**

```python
from flask import Flask

app = Flask(__name__)


@app.route("/")
def home():
    return "Home"


@app.route("/about")
def about():
    return "About us"


@app.route("/users/<int:user_id>")
def user(user_id):
    return f"User {user_id}"
```

Other frameworks follow the same shape. Each of these is a one-line registration of a path:

```php
// Laravel (PHP)
Route::get('/about', fn () => view('about'));
```

```python
# Django (Python), in urls.py
urlpatterns = [
    path("about/", views.about),
]
```

```java
// Spring Boot (Java)
@GetMapping("/about")
public String about() { return "about"; }
```

```csharp
// ASP.NET Core (C#), minimal API
app.MapGet("/about", () => "About us");
```

Notice that backend routes are tied to an **HTTP method** (`GET`, `POST`, and so on, covered in Lesson 12). The same path can do different things for different methods.

### Style 2: A route table of components

Client-side frameworks map paths to **components**. For example, React Router declares routes like this:

```jsx
<Routes>
  <Route path="/" element={<Home />} />
  <Route path="/about" element={<About />} />
</Routes>
```

Angular and Vue.js use the same idea with a configuration array that lists a `path` and the component to show for it.

### Style 3: File-based routing

In **file-based routing**, you do not write a route table. The **folder and file structure** defines the URLs (as shown in Lesson 4). Next.js, SvelteKit, and Nuxt work this way:

```text
app/page.js              →  /
app/about/page.js        →  /about
app/contact/page.js      →  /contact
```

Adding a page is as simple as adding a file.

### The fallback (404) route

A good router also decides what happens when *nothing* matches. Frameworks return a "404 Not Found" response by default, and let you customize it with a friendly "page not found" screen. Many SPA frameworks use a **wildcard** route (matching any path) at the end of the route table for this purpose.

---

## 10.3 Dynamic and Nested Routes

### Dynamic routes (route parameters)

You cannot define a separate route for every user, product, or blog post. A **dynamic route** contains a placeholder (a **route parameter**) that matches many different URLs and hands the matched value to your code.

| Framework | Syntax for "user id" in the path | Reading the value |
|---|---|---|
| Express | `/users/:id` | `req.params.id` |
| Flask | `/users/<int:user_id>` | A function argument |
| Django | `users/<int:id>/` | A view argument |
| Laravel | `/users/{id}` | A controller argument |
| FastAPI and ASP.NET Core | `/users/{id}` | A function argument |
| Next.js (file-based) | A folder named `[id]` | From the route's `params` |
| React Router | `/users/:id` | A hook such as `useParams()` |

In the Express example from 10.2, requesting `/users/42` makes `req.params.id` equal to `"42"`. Flask's `<int:user_id>` goes one step further and only matches whole numbers, so `/users/abc` correctly results in a 404.

### Path parameters vs query strings

There are two ways to pass information in a URL:

| | Example | Best used for |
|---|---|---|
| **Path parameter** | `/users/42` | Identifying *which resource* you want |
| **Query string** | `/users?role=admin&page=2` | Optional settings such as filters, sorting, and pagination |

In the Express example, `/users/42?tab=posts` uses both: `42` is a path parameter and `tab=posts` is a query value read with `req.query.tab`.

### Nested routes and layouts

Many apps have **shared page frames**, such as a header and sidebar that stay in place while the main content changes. **Nested routes** express this: a parent route renders the shared frame, and its child routes render inside a marked slot.

```text
/dashboard                  →  DashboardLayout + DashboardHome
/dashboard/settings         →  DashboardLayout + SettingsPage
/dashboard/settings/profile →  DashboardLayout + SettingsLayout + ProfilePage
```

The marked slot has a different name depending on the framework: `<Outlet />` in React Router, `<router-outlet>` in Angular, `<RouterView />` in Vue.js, and a `layout` file in Next.js. The layout stays mounted while only the inner part swaps.

### Catch-all routes

Some routes need to match an unknown number of path segments (for example, `/docs/guides/getting-started/install`). **Catch-all** or **wildcard** routes exist for this, using syntax such as `[...slug]` in Next.js or `*` in many routers.

### Protected routes (guards)

Some routes should only be available to certain users. A **route guard** is a check that runs before a route is entered, redirecting to a login page when the user is not signed in. Angular has built-in guards, React apps commonly wrap routes in a small component, and backend frameworks use middleware for the same purpose (Lesson 13). Remember that a guard in the browser only improves the user experience: **real access control must be enforced on the server** (Lesson 15).

---

## 10.4 Navigation in Mobile Apps

Mobile apps do not have a URL bar, and users move between screens differently than on the web. Mobile frameworks use their own navigation model.

### The navigation stack

The core concept is a **stack** of screens:

- **Push**: opening a new screen places it on top of the stack.
- **Pop**: going back removes the top screen, revealing the previous one.

```text
 Home            Home            Home
              ┌─────────┐     ┌─────────┐
              │ Details │     │ Details │
              └─────────┘     │ Edit    │
                              └─────────┘
 (start)      push Details     push Edit      →  pressing Back pops "Edit"
```

This matches how people expect the system **Back** button or swipe gesture to behave.

### Common navigation patterns

| Pattern | Description | Typical use |
|---|---|---|
| **Stack** | Screens pushed on top of each other | Drilling into details |
| **Tabs** | A bar at the bottom or top for switching between top-level sections | Main areas of the app (Home, Search, Profile) |
| **Drawer** | A menu that slides in from the side | Many top-level destinations |
| **Modal** | A screen presented over the current one | Short tasks such as compose or confirm |

### Navigation in Flutter

Flutter provides a `Navigator` that manages the stack of screens:

```dart
// Open a new screen
Navigator.push(
  context,
  MaterialPageRoute(builder: (context) => const DetailScreen()),
);

// Go back
Navigator.pop(context);
```

For larger apps, Flutter developers often use a routing package (such as `go_router`) that supports named paths and deep links.

### Navigation in React Native

React Native does not include a router in its core. Most apps use a library such as **React Navigation**, which offers *stack*, *tab*, and *drawer* navigators that you combine, and screens receive parameters from the screen that opened them. Some projects use a file-based approach similar to Next.js instead.

### Deep links

A **deep link** is a URL that opens a specific screen inside the app (for example, a link to a particular product in a shopping app). Mobile navigation libraries can map such links to screens, which also allows notifications and other apps to open the right place directly.

### Web vs mobile at a glance

| | Web | Mobile |
|---|---|---|
| **Location indicator** | URL in the address bar | The screen stack and tabs |
| **Back behavior** | Browser history | Pop the navigation stack |
| **Entry points** | Any URL | The launcher, notifications, or deep links |
| **Typical tools** | React Router, Angular Router, Next.js | Flutter Navigator, React Navigation |

---

[Previous](./[9]-State-Management.md) | [Table of Contents](./[0]-Introduction-to-Frameworks.md) | [Next](./[11]-Styling-And-User-Interfaces.md)
