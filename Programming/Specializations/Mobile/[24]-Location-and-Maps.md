[Previous](./[23]-Camera-and-Media-Access.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[25]-Push-Notifications.md)

*Device Features*

# Lesson 24 - Location & Maps

## 24.1 Requesting Location Permission

Like the camera, location access requires a declared permission and a runtime prompt. iOS and Android both offer graded permission levels:

- **While using the app** — location is only available when the app is in the foreground.
- **Always** — location is available in the background too (requires stronger justification and extra review scrutiny from app stores, since it's more privacy-sensitive).

---

## 24.2 Getting the Current Location

```dart
Position position = await Geolocator.getCurrentPosition();
print("${position.latitude}, ${position.longitude}");
```

For continuously updating location (e.g., live navigation), apps subscribe to a location **stream** instead of polling once, which is more battery-efficient since the OS can batch and optimize updates.

---

## 24.3 Displaying Maps

Embedding an interactive map is typically done via a platform map SDK wrapped in a widget:

```dart
GoogleMap(
  initialCameraPosition: CameraPosition(target: LatLng(30.27, -97.74), zoom: 12),
  markers: {Marker(markerId: MarkerId('here'), position: myPosition)},
)
```

Common map features include markers (pins), polylines (drawn routes), and camera controls (panning/zooming programmatically).

---

## 24.4 Geocoding

**Geocoding** converts a human-readable address into coordinates ("123 Main St" → lat/lng), and **reverse geocoding** does the opposite (coordinates → a readable address). Both are common in apps with delivery addresses, check-ins, or location search.

## 24.5 Battery and Accuracy Tradeoffs

Higher location accuracy consumes significantly more battery (GPS vs. network-based positioning). Apps should request only the accuracy level they actually need — a weather app checking the user's city doesn't need GPS-precision location, while a turn-by-turn navigation app does.

[Previous](./[23]-Camera-and-Media-Access.md) | [Table of Contents](./[0]-Introduction-to-Mobile-Development.md) | [Next](./[25]-Push-Notifications.md)
