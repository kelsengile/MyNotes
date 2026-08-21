[Previous](./[31]-Performance-Optimization.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[33]-Web-Accessibility.md)

*Deployment & Production*

# Lesson 32 - Progressive Web Apps

## 32.1 What a PWA Is

A **Progressive Web App (PWA)** is a website built to behave like a native app — installable on a device's home screen, capable of working offline, and able to send push notifications — while still being a normal website underneath, built with HTML, CSS, and JavaScript, and reachable through a regular URL.

---

## 32.2 The Web App Manifest

A **manifest** file (`manifest.json`) tells the browser how the app should behave when installed:

```json
{
  "name": "My App",
  "short_name": "MyApp",
  "start_url": "/",
  "display": "standalone",
  "icons": [
    { "src": "/icon-192.png", "sizes": "192x192", "type": "image/png" }
  ],
  "theme_color": "#3498db"
}
```

```html
<link rel="manifest" href="/manifest.json" />
```

`"display": "standalone"` makes the installed app open without the browser's usual address bar and UI chrome, so it looks more like a native app.

---

## 32.3 Service Workers

A **service worker** is a JavaScript file that runs separately from the page, in the background, and can intercept network requests — enabling offline support by serving cached responses when there's no connection:

```js
// sw.js
self.addEventListener("install", (event) => {
  event.waitUntil(
    caches.open("v1").then(cache => cache.addAll(["/", "/style.css", "/script.js"]))
  );
});

self.addEventListener("fetch", (event) => {
  event.respondWith(
    caches.match(event.request).then(response => response || fetch(event.request))
  );
});
```

```js
// registering it from your main app
navigator.serviceWorker.register("/sw.js");
```

---

## 32.4 Installability

When a site meets certain criteria (served over HTTPS, has a valid manifest, registers a service worker), browsers offer users an "Install" prompt, adding the app to their home screen or app list without going through an app store at all.

---

## 32.5 When a PWA Makes Sense

PWAs are a strong fit for content-focused or utility apps that benefit from offline access and quick reopening (news readers, to-do apps, tools) without the overhead of building and maintaining separate native iOS/Android apps. They're not a full replacement for native apps when deep device integration (advanced camera control, certain background processing, App Store distribution requirements) is required.

---

[Previous](./[31]-Performance-Optimization.md) | [Table of Contents](./[0]-Introduction-to-Website-Development.md) | [Next](./[33]-Web-Accessibility.md)
