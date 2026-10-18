[Previous](./[10]-Styling-In-Nextjs.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[12]-Middleware-And-Edge.md)

*Building Features*

# Lesson 11 - Optimization And Metadata

## 11.1 next/image

The `<Image>` component from `next/image` extends the HTML `<img>` tag with automatic optimization:

- Serves modern formats (WebP/AVIF) when supported
- Resizes images for the requesting device
- Lazy-loads images by default
- Prevents **layout shift** by reserving space

**Local images** know their dimensions automatically:

```tsx
import Image from "next/image";
import hero from "@/public/hero.jpg";

export default function Hero() {
  return <Image src={hero} alt="A mountain at sunrise" priority />;
}
```

**Remote images** need explicit `width` and `height`, and the host must be allowed in the config:

```tsx
<Image
  src="https://images.example.com/photo.jpg"
  alt="Team photo"
  width={800}
  height={600}
/>
```

```ts
// next.config.ts
import type { NextConfig } from "next";

const nextConfig: NextConfig = {
  images: {
    remotePatterns: [
      { protocol: "https", hostname: "images.example.com" },
    ],
  },
};

export default nextConfig;
```

Tips:

- Always provide meaningful `alt` text.
- Add `priority` to the main above-the-fold image (such as the hero) so it loads eagerly.
- Use `fill` with a positioned parent for images that should cover a container, together with the `sizes` prop.

---

## 11.2 next/font

`next/font` loads fonts with no layout shift and no external network requests at runtime. Font files are downloaded at build time and served from your own domain.

**Google fonts:**

```tsx
import { Roboto } from "next/font/google";

const roboto = Roboto({
  subsets: ["latin"],
  weight: ["400", "700"],
  display: "swap",
});

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en" className={roboto.className}>
      <body>{children}</body>
    </html>
  );
}
```

**Local fonts:**

```tsx
import localFont from "next/font/local";

const brand = localFont({
  src: "./fonts/Brand.woff2",
  display: "swap",
});
```

**CSS variable approach** (works well with Tailwind):

```tsx
const inter = Inter({ subsets: ["latin"], variable: "--font-inter" });
// <html className={inter.variable}>
```

Variable fonts are recommended because one file covers multiple weights, improving performance.

---

## 11.3 Metadata and SEO

Metadata controls the page title, description, and social sharing previews. Export a `metadata` object from a `layout.tsx` or `page.tsx` (Server Components only):

```tsx
// app/layout.tsx
import type { Metadata } from "next";

export const metadata: Metadata = {
  title: {
    default: "My Site",
    template: "%s | My Site",
  },
  description: "A site about learning Next.js.",
  openGraph: {
    title: "My Site",
    description: "A site about learning Next.js.",
    images: ["/og-image.png"],
  },
};
```

For **dynamic metadata** based on route params or fetched data, export `generateMetadata`:

```tsx
// app/blog/[slug]/page.tsx
import type { Metadata } from "next";

export async function generateMetadata({
  params,
}: {
  params: Promise<{ slug: string }>;
}): Promise<Metadata> {
  const { slug } = await params;
  const post = await getPost(slug);
  return { title: post.title, description: post.summary };
}
```

Special metadata files placed in `app/`:

| File | Purpose |
|---|---|
| `sitemap.ts` | Generates `/sitemap.xml` |
| `robots.ts` | Generates `/robots.txt` |
| `favicon.ico` | Site icon |
| `opengraph-image.tsx` | Generated social preview image |

```ts
// app/sitemap.ts
import type { MetadataRoute } from "next";

export default function sitemap(): MetadataRoute.Sitemap {
  return [
    { url: "https://example.com", lastModified: new Date() },
    { url: "https://example.com/about", lastModified: new Date() },
  ];
}
```

Good SEO also relies on semantic HTML (`<h1>`, `<main>`, `<nav>`), fast loading, and correct status codes.

---

## 11.4 Scripts and third-party code

Use the `<Script>` component from `next/script` to load external JavaScript (analytics, chat widgets, ads) without hurting performance.

```tsx
import Script from "next/script";

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body>
        {children}
        <Script
          src="https://example.com/analytics.js"
          strategy="afterInteractive"
        />
      </body>
    </html>
  );
}
```

The `strategy` prop controls when the script loads:

| Strategy | When it loads | Use for |
|---|---|---|
| `beforeInteractive` | Before the page becomes interactive | Critical scripts (bot detection, consent) |
| `afterInteractive` (default) | Soon after the page becomes interactive | Analytics, tag managers |
| `lazyOnload` | During browser idle time | Chat widgets, low-priority scripts |

Inline scripts need an `id`:

```tsx
<Script id="init" strategy="afterInteractive">
  {`console.log("loaded")`}
</Script>
```

Use `onLoad`, `onReady`, and `onError` callbacks (in Client Components) to react to script events. For popular services such as Google Analytics and Google Tag Manager, the `@next/third-parties` package offers optimized ready-made components.

[Previous](./[10]-Styling-In-Nextjs.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[12]-Middleware-And-Edge.md)