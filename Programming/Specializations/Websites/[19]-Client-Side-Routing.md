[Previous](./[18]-React-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[20]-State-Management.md)

*Front-End Frameworks*

# Lesson 19 - Client-Side Routing

## 19.1 Multi-Page Sites vs Single-Page Apps

Traditionally, every URL corresponds to a separate HTML file requested fresh from the server — clicking a link triggers a full page reload. A **Single-Page Application (SPA)** instead loads one HTML shell once, then uses JavaScript to swap out content as the user navigates, without a full page reload. **Client-side routing** is what makes the URL bar still update correctly and reflect what's on screen, even though the browser never actually requested a new page.

---

## 19.2 The History API

Client-side routers are built on the browser's **History API**, which lets JavaScript change the URL and add browser-history entries without triggering a navigation:

```js
history.pushState({}, "", "/about");
window.addEventListener("popstate", () => {
  // handle back/forward button
});
```

Routing libraries wrap this API so you rarely touch it directly.

---

## 19.3 Routing in React

**React Router** is the most common routing library for React:

```jsx
import { BrowserRouter, Routes, Route, Link } from "react-router-dom";

function App() {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
      </nav>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </BrowserRouter>
  );
}
```

`<Link>` renders a real `<a>` tag but intercepts the click to route client-side instead of reloading the page.

---

## 19.4 Dynamic Routes and Parameters

Routes can capture dynamic segments, useful for detail pages like a specific user or product:

```jsx
<Route path="/users/:id" element={<UserProfile />} />

function UserProfile() {
  const { id } = useParams();
  // fetch and display the user matching `id`
}
```

---

## 19.5 The Server-Side Gap

Because routing happens entirely in JavaScript, a server needs to be configured to return the same `index.html` for every route (a "fallback" or "rewrite" rule) so that a direct visit or refresh on `/about` still loads the app instead of a `404`. This is a common gotcha when deploying SPAs (Lesson 28) and is different from traditional server-rendered routing, where the server genuinely has a separate handler for each path.

---

[Previous](./[18]-React-Fundamentals.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[20]-State-Management.md)
