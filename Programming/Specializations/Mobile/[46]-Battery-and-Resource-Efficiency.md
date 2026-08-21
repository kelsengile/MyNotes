[Previous](./[45]-Mobile-Security-Basics.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md)

*Best Practices*

# Lesson 46 - Battery & Resource Efficiency

## 46.1 Why Battery Efficiency Matters

An app that drains a user's battery quickly is one of the fastest ways to earn an uninstall — both platforms actively surface battery usage per app to users (iOS Battery settings, Android's Battery usage screen), and repeat offenders get flagged directly to the user.

---

## 46.2 Location and Sensor Usage

Continuous GPS/location tracking and constant sensor polling (accelerometer, gyroscope) are among the single largest battery drains an app can cause:

- Request the **lowest accuracy and update frequency** that satisfies the feature's actual needs — a step counter doesn't need GPS-precision updates every second.
- Stop location/sensor updates as soon as they're no longer needed (e.g. when a screen using them is backgrounded), rather than leaving them running indefinitely.
- Prefer **geofencing** (getting notified only when the user enters/exits a defined area) over continuous tracking when the use case allows it.

---

## 46.3 Background Work Efficiency

As covered in Lesson 28, background tasks should be batched and scheduled by the OS (`BGTaskScheduler` on iOS, `WorkManager` on Android) rather than run via custom polling loops — the OS batches and times these tasks system-wide across all apps to minimize total battery impact, something an app can't replicate on its own.

---

## 46.4 Network Efficiency

Radio usage (cellular/Wi-Fi) has a real battery cost even for small requests, because waking the radio from an idle state costs more energy than the transfer itself:

- Batch multiple small requests into fewer, larger ones where possible.
- Avoid frequent short-interval polling; prefer push notifications or WebSockets for real-time updates instead of repeatedly asking "anything new?"
- Cache aggressively (Lesson 17) to avoid redundant fetches of unchanged data.

---

## 46.5 CPU and Rendering Efficiency

Unnecessary CPU work drains battery just as directly as networking or GPS:

- Avoid busy-loops or overly frequent timers polling for changes; prefer reactive/event-driven patterns instead.
- Reduce unnecessary re-renders — in declarative UI frameworks, re-computing a view that hasn't actually changed wastes both CPU and battery (see Lesson 15 on managing UI state efficiently).
- Release resources (camera sessions, animations, timers) as soon as a screen is no longer visible rather than leaving them running in the background.

---

## 46.6 Wrapping Up

Battery and resource efficiency closes the loop with performance (Lesson 44) and background work (Lesson 28) — a genuinely well-built mobile app is fast, responsive, and easy on the device's battery all at once, which together make up much of what separates a polished app from a merely functional one. That brings this course full circle, from your first "Hello World" screen all the way through to shipping and maintaining a production-quality mobile app.

[Previous](./[45]-Mobile-Security-Basics.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md)
