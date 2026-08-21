[Previous](./[27]-Deep-Linking-and-App-Links.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[29]-Localization-and-Internationalization.md)

*Background & System Integration*

# Lesson 28 - Background Tasks & Services

## 28.1 The App Lifecycle

Mobile apps move through distinct **lifecycle states** managed by the OS: foreground (active, visible), background (not visible but still in memory), and terminated (fully closed). The OS aggressively manages background apps to save battery and memory, which is why mobile background execution is far more restricted than on desktop.

```dart
class _MyAppState extends State<MyApp> with WidgetsBindingObserver {
  @override
  void didChangeAppLifecycleState(AppLifecycleState state) {
    if (state == AppLifecycleState.paused) pauseVideoPlayback();
    if (state == AppLifecycleState.resumed) refreshData();
  }
}
```

---

## 28.2 Why Background Work is Restricted

If apps could run unlimited work in the background, phones would drain their batteries in hours. Both iOS and Android impose strict limits and require you to use specific system-provided APIs for background work rather than just running an infinite loop.

---

## 28.3 Background Work APIs

- **iOS**: Background Tasks framework (`BGTaskScheduler`) for periodic refresh or processing; background modes for specific cases like audio playback or location tracking.
- **Android**: **WorkManager**, Google's recommended API for deferrable, guaranteed background work (e.g., "sync data sometime in the next few hours, ideally on wifi") — it survives app restarts and respects battery-saving OS constraints automatically.

```kotlin
val syncRequest = PeriodicWorkRequestBuilder<SyncWorker>(1, TimeUnit.HOURS).build()
WorkManager.getInstance(context).enqueue(syncRequest)
```

---

## 28.4 Common Background Use Cases

- Syncing data periodically (e.g., downloading new emails).
- Uploading queued content once connectivity returns.
- Playing audio (music/podcast apps get special background audio privileges).
- Tracking location for fitness or navigation apps (requires the "Always" permission from Lesson 24.1 and stronger justification during app review).

[Previous](./[27]-Deep-Linking-and-App-Links.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[29]-Localization-and-Internationalization.md)
