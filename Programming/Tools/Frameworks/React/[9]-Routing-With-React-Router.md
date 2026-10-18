[Previous](./[8]-Context-And-Global-State.md) | [Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[10]-Data-Fetching-And-APIs.md)

*Building Applications*

# Lesson 9 - Routing With React Router

## 9.1 Client-side routing

A traditional website loads a new HTML page for every URL. In a single-page app there is only one HTML page, so **client-side routing** changes what is displayed based on the URL, **without a full page reload**. The router listens for URL changes (using the browser History API), then renders the matching components.

Benefits: instant navigation, preserved app state, and shareable, bookmarkable URLs.

React itself has no router. The most widely used one is **React Router**. This lesson uses **React Router v7** in its **library (declarative/data) mode**, where you install a single package:

```bash
npm install react-router
```

In v7, everything is imported from `"react-router"`. In older versions (v6 and earlier) the browser pieces came from `"react-router-dom"`. If you see that in older tutorials, the concepts are the same.

---

## 9.2 Defining routes and links

### Defining routes

Create a router with `createBrowserRouter`, then render it with `RouterProvider`:

```jsx
// main.jsx
import { StrictMode } from "react";
import { createRoot } from "react-dom/client";
import { createBrowserRouter, RouterProvider } from "react-router";
import Home from "./pages/Home.jsx";
import About from "./pages/About.jsx";
import NotFound from "./pages/NotFound.jsx";

const router = createBrowserRouter([
  { path: "/", element: <Home /> },
  { path: "/about", element: <About /> },
  { path: "*", element: <NotFound /> },        // catch-all for unknown URLs
]);

createRoot(document.getElementById("root")).render(
  <StrictMode>
    <RouterProvider router={router} />
  </StrictMode>
);
```

Each route object pairs a URL `path` with the `element` to display.

### Links

Never use `<a href>` for internal navigation, since it causes a full page reload. Use `Link`:

```jsx
import { Link } from "react-router";

function Navbar() {
  return (
    <nav>
      <Link to="/">Home</Link>
      <Link to="/about">About</Link>
    </nav>
  );
}
```

### NavLink

`NavLink` is a `Link` that knows whether it matches the current URL. It adds an `active` class by default and also accepts a function:

```jsx
import { NavLink } from "react-router";

<NavLink
  to="/about"
  className={({ isActive }) => (isActive ? "link active" : "link")}
>
  About
</NavLink>
```

### Navigating from code

Use the `useNavigate` hook, for example after a form submit:

```jsx
import { useNavigate } from "react-router";

function LoginForm() {
  const navigate = useNavigate();

  function handleSubmit(e) {
    e.preventDefault();
    // ...log the user in...
    navigate("/dashboard");
  }

  return <form onSubmit={handleSubmit}>{/* ... */}</form>;
}
```

---

## 9.3 Dynamic and nested routes

### Dynamic routes (URL parameters)

A segment that starts with `:` is a **parameter**. Read it with `useParams`:

```jsx
const router = createBrowserRouter([
  { path: "/users/:userId", element: <UserPage /> },
]);
```

```jsx
import { useParams } from "react-router";

function UserPage() {
  const { userId } = useParams();     // always a string
  return <h1>User #{userId}</h1>;
}
```

Visiting `/users/42` renders `User #42`.

### Query strings

For `?sort=price&page=2`, use `useSearchParams`:

```jsx
import { useSearchParams } from "react-router";

function Products() {
  const [params, setParams] = useSearchParams();
  const sort = params.get("sort") ?? "name";

  return (
    <button onClick={() => setParams({ sort: "price" })}>
      Sorted by {sort}
    </button>
  );
}
```

### Nested routes and layouts

Routes can have `children`. The parent renders shared UI (a header, sidebar) and an **`<Outlet />`** marks where the matching child appears:

```jsx
import { Outlet, NavLink } from "react-router";

function RootLayout() {
  return (
    <>
      <header>
        <NavLink to="/">Home</NavLink>
        <NavLink to="/products">Products</NavLink>
      </header>
      <main>
        <Outlet />
      </main>
    </>
  );
}
```

```jsx
const router = createBrowserRouter([
  {
    path: "/",
    element: <RootLayout />,
    children: [
      { index: true, element: <Home /> },                 // matches "/"
      { path: "products", element: <ProductList /> },     // "/products"
      { path: "products/:id", element: <ProductDetail /> } // "/products/7"
    ],
  },
  { path: "*", element: <NotFound /> },
]);
```

Key points:

- Child `path` values are **relative** to the parent (no leading slash).
- An **`index` route** is the default child shown when the URL matches exactly the parent's path.
- Layouts keep their state when you navigate between their children, because they stay mounted.

---

## 9.4 Protected routes and redirects

### Redirecting

Render `<Navigate>` to send the user elsewhere:

```jsx
import { Navigate } from "react-router";

<Navigate to="/login" replace />
```

`replace` swaps the current history entry instead of adding one, so the Back button doesn't return to the page that redirected.

You can also redirect a path in the route table:

```jsx
{ path: "/old-page", element: <Navigate to="/new-page" replace /> }
```

### Protected routes

A **protected route** only shows its content to authorized users. Create a wrapper layout route that checks authentication and renders either an `Outlet` or a redirect:

```jsx
import { Navigate, Outlet, useLocation } from "react-router";
import { useAuth } from "./AuthContext.jsx";   // your own auth context (see Lesson 8)

function ProtectedRoute() {
  const { user } = useAuth();
  const location = useLocation();

  if (!user) {
    return <Navigate to="/login" replace state={{ from: location }} />;
  }
  return <Outlet />;
}
```

Use it as a pathless layout route so everything inside it is protected:

```jsx
const router = createBrowserRouter([
  { path: "/login", element: <Login /> },
  {
    element: <ProtectedRoute />,          // no path: just wraps its children
    children: [
      { path: "/dashboard", element: <Dashboard /> },
      { path: "/settings", element: <Settings /> },
    ],
  },
]);
```

### Sending the user back after login

The `state` passed in `<Navigate>` is available through `useLocation`. In the login page:

```jsx
function Login() {
  const navigate = useNavigate();
  const location = useLocation();
  const from = location.state?.from?.pathname ?? "/dashboard";

  function handleLogin() {
    // ...sign in...
    navigate(from, { replace: true });
  }

  return <button onClick={handleLogin}>Log in</button>;
}
```

### Important security note

Client-side route protection is only a **user experience** feature. Anyone can inspect or modify code running in their browser. The **server must always check authorization** for any protected data or action.

### Deploying note

Because all URLs are handled by the single `index.html`, the host must be configured to serve `index.html` for unknown paths. See [Lesson 17](./[17]-Building-And-Deployment.md).

[Previous](./[8]-Context-And-Global-State.md) | [Table of Contents](./[0]-Introduction-to-React.md) | [Next](./[10]-Data-Fetching-And-APIs.md)