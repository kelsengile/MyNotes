[Previous](./[16]-Testing-Nextjs-Applications.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[18]-Deployment.md)

*Quality And Delivery*

# Lesson 17 - Performance Tuning

## 17.1 Core Web Vitals

**Core Web Vitals** are Google's metrics for real-world user experience. They also influence search ranking.

| Metric | Measures | Good target |
|---|---|---|
| **LCP** (Largest Contentful Paint) | How fast the main content appears | 2.5 s or less |
| **INP** (Interaction to Next Paint) | How quickly the page responds to input | 200 ms or less |
| **CLS** (Cumulative Layout Shift) | How much the layout jumps around | 0.1 or less |

How Next.js features help:

- **LCP**: use static rendering or streaming, `next/image` with `priority` on the hero image, and `next/font`.
- **INP**: ship less client JavaScript (prefer Server Components), avoid heavy work on interactions, and split large components.
- **CLS**: `next/image` reserves space for images, `next/font` avoids font swapping jumps, and you should give embeds and ads fixed dimensions.

Measure with:

- **Lighthouse** in Chrome DevTools (lab data)
- **PageSpeed Insights** (lab plus real-user data)
- Real-user monitoring in production (see 17.4)

Always test with a **production build** (`npm run build && npm run start`). The dev server is slower and not representative.

---

## 17.2 Bundle analysis

Less JavaScript means faster pages. **`@next/bundle-analyzer`** shows what is inside your bundles.

```bash
npm install -D @next/bundle-analyzer
```

```ts
// next.config.mjs
import bundleAnalyzer from "@next/bundle-analyzer";

const withBundleAnalyzer = bundleAnalyzer({
  enabled: process.env.ANALYZE === "true",
});

const nextConfig = {};

export default withBundleAnalyzer(nextConfig);
```

```bash
ANALYZE=true npm run build
```

This opens an interactive treemap. (On Windows PowerShell, set the variable first, for example `$env:ANALYZE="true"; npm run build`.) Look for:

- Very large libraries (date libraries, charting libraries, icon packs)
- Duplicate packages
- Server-only code accidentally included in the client bundle

Ways to shrink bundles:

- Keep logic in **Server Components** so its code is never sent to the browser.
- Replace heavy libraries with lighter ones (for example, a small date library instead of a large one).
- Import only what you need: `import { debounce } from "lodash-es"`, not the whole library.
- Keep `"use client"` boundaries small (Lesson 4).

The `next build` output also lists the first-load JavaScript size for each route.

---

## 17.3 Lazy loading

**Lazy loading** delays loading code or content until it is needed.

**Dynamic imports** with `next/dynamic` split a component into its own chunk:

```tsx
"use client";

import dynamic from "next/dynamic";

const HeavyChart = dynamic(() => import("./HeavyChart"), {
  loading: () => <p>Loading chart...</p>,
});

export default function Report() {
  return <HeavyChart />;
}
```

Disable server rendering for components that need browser APIs. (`ssr: false` is only allowed inside Client Components.)

```tsx
const Map = dynamic(() => import("./Map"), { ssr: false });
```

**Load on demand** only when the user needs it:

```tsx
"use client";

import { useState } from "react";
import dynamic from "next/dynamic";

const Editor = dynamic(() => import("./Editor"));

export default function Page() {
  const [show, setShow] = useState(false);
  return (
    <>
      <button onClick={() => setShow(true)}>Open editor</button>
      {show && <Editor />}
    </>
  );
}
```

Other forms of lazy loading:

- `next/image` lazy-loads images below the fold automatically.
- `<Suspense>` streams slow server content (Lesson 5).
- `next/script` with `lazyOnload` defers third-party scripts (Lesson 11).

Do not lazy-load content that appears at the top of the page, because it can hurt LCP.

---

## 17.4 Monitoring

You cannot improve what you do not measure. Monitoring tells you how real users experience the app and where it fails.

**Report Web Vitals** from real users:

```tsx
// app/components/WebVitals.tsx
"use client";

import { useReportWebVitals } from "next/web-vitals";

export function WebVitals() {
  useReportWebVitals((metric) => {
    // send to your analytics endpoint
    navigator.sendBeacon("/api/vitals", JSON.stringify(metric));
  });
  return null;
}
```

Render `<WebVitals />` once in the root layout.

**Tracing and logs** with OpenTelemetry. Next.js supports an `instrumentation.ts` file at the project root that runs when the server starts:

```ts
// instrumentation.ts
export async function register() {
  // initialize your observability SDK here (for example OpenTelemetry)
}
```

Common tools:

| Need | Examples |
|---|---|
| Real-user performance | Vercel Analytics / Speed Insights, Google Search Console |
| Error tracking | Sentry |
| Tracing and metrics | OpenTelemetry, Datadog, New Relic |
| Uptime checks | Better Stack, UptimeRobot |

A good habit is to set a **performance budget** (for example, a maximum JavaScript size per route), then check it in CI and review monitoring dashboards after each release.

[Previous](./[16]-Testing-Nextjs-Applications.md) | [Table of Contents](./[0]-Introduction-to-Next.js.md) | [Next](./[18]-Deployment.md)