[Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[2]-Routing-With-The-App-Router.md)

*Basics*

# Lesson 1 - What Is Next.js

## 1.1 Meta-frameworks explained

A **library** (like React) solves one problem: building user interfaces. A **framework** makes many decisions for you. A **meta-framework** is built on top of a library and adds everything needed to ship a real application.

React alone does not include:

- A router (how URLs map to pages)
- A way to render on the server
- A build and bundling setup
- A data loading convention
- A backend or API layer

Next.js supplies all of these. You still write React components, but Next.js decides how they are routed, rendered, bundled, and served.

---

## 1.2 Next.js vs plain React

| Concern | Plain React (e.g. Vite) | Next.js |
|---|---|---|
| Routing | Add a library (React Router) | Built in, based on folders |
| Rendering | Client-side only by default | Server, static, or client |
| SEO | Weak (empty HTML shell until JS runs) | Strong (HTML is pre-rendered) |
| Backend | Separate server needed | Route handlers and server actions |
| Image/font optimization | Manual | Built in |
| Configuration | You assemble it | Sensible defaults |

With plain React, the browser downloads JavaScript, runs it, and only then shows content. With Next.js, the server can send ready-made HTML first, so users see content sooner and search engines can read it. React then "hydrates" the page, attaching interactivity to the existing HTML.

The trade-off is that Next.js has more concepts to learn (server vs client components, caching), and it is more tightly tied to a server or platform than a purely static React app.

---

## 1.3 Creating a project with create-next-app

You need Node.js installed (check the Next.js docs for the minimum supported version). Create a project with:

```bash
npx create-next-app@latest my-app
```

The installer asks several questions:

- Would you like to use TypeScript?
- Would you like to use ESLint?
- Would you like to use Tailwind CSS?
- Would you like your code inside a `src/` directory?
- Would you like to use App Router? (choose **Yes**)
- Would you like to customize the import alias?

Then start the dev server:

```bash
cd my-app
npm run dev
```

Open `http://localhost:3000`. Editing a file updates the browser instantly through Fast Refresh.

The scripts in `package.json`:

| Script | Purpose |
|---|---|
| `npm run dev` | Development server with hot reload |
| `npm run build` | Create an optimized production build |
| `npm run start` | Run the production build |
| `npm run lint` | Run the linter |

---

## 1.4 Tour of the project structure

A fresh App Router project looks like this:

```
my-app/
├── app/
│   ├── layout.tsx      # Root layout (wraps every page)
│   ├── page.tsx        # The home page ("/")
│   ├── globals.css     # Global styles
│   └── favicon.ico
├── public/             # Static files served from "/"
├── next.config.ts      # Next.js configuration
├── package.json
├── tsconfig.json
└── node_modules/
```

- **`app/`** holds your routes. Folders become URL segments.
- **`app/layout.tsx`** is required. It must render `<html>` and `<body>` and accept `children`.
- **`app/page.tsx`** is the UI for the `/` route.
- **`public/`** holds static assets. A file at `public/logo.png` is available at `/logo.png`.
- **`next.config.ts`** (or `.js`/`.mjs`) controls framework settings such as redirects, image domains, and output mode.

A minimal page:

```tsx
// app/page.tsx
export default function HomePage() {
  return <h1>Hello, Next.js!</h1>;
}
```

A minimal root layout:

```tsx
// app/layout.tsx
export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
```

Every page you create is rendered inside this layout.

[Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[2]-Routing-With-The-App-Router.md)