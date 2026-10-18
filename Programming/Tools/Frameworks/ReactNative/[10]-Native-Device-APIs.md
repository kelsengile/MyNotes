[Previous](./[9]-State-Management.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[11]-Animations-And-Gestures.md)

*Device Features*

# Lesson 10 - Native Device APIs

Phones have hardware that web pages cannot easily reach: the camera, GPS, sensors, and notifications. In Expo, these features are provided as installable packages that you call from JavaScript.

## 10.1 Camera and media

**Take photos or video with `expo-camera`**

```bash
npx expo install expo-camera
```

```jsx
import { useRef, useState } from 'react';
import { CameraView, useCameraPermissions } from 'expo-camera';
import { View, Text, Button, Image } from 'react-native';

export default function CameraScreen() {
  const [permission, requestPermission] = useCameraPermissions();
  const [facing, setFacing] = useState('back');
  const [photo, setPhoto] = useState(null);
  const cameraRef = useRef(null);

  if (!permission) return <View />; // still loading

  if (!permission.granted) {
    return (
      <View style={{ flex: 1, justifyContent: 'center' }}>
        <Text>We need your permission to use the camera.</Text>
        <Button title="Grant permission" onPress={requestPermission} />
      </View>
    );
  }

  const takePicture = async () => {
    const result = await cameraRef.current?.takePictureAsync();
    setPhoto(result);
  };

  if (photo) {
    return <Image source={{ uri: photo.uri }} style={{ flex: 1 }} />;
  }

  return (
    <View style={{ flex: 1 }}>
      <CameraView ref={cameraRef} style={{ flex: 1 }} facing={facing} />
      <Button title="Flip" onPress={() => setFacing((f) => (f === 'back' ? 'front' : 'back'))} />
      <Button title="Take photo" onPress={takePicture} />
    </View>
  );
}
```

`CameraView` can also scan barcodes and QR codes and record video.

**Pick photos from the library with `expo-image-picker`**

```jsx
import * as ImagePicker from 'expo-image-picker';

const pickImage = async () => {
  const result = await ImagePicker.launchImageLibraryAsync({
    mediaTypes: ['images'],
    allowsEditing: true,
    quality: 0.8,
  });

  if (!result.canceled) {
    console.log(result.assets[0].uri);
  }
};
```

Other media packages: `expo-av` and `expo-video` / `expo-audio` for playback, and `expo-media-library` to save files to the gallery.

---

## 10.2 Location

Use `expo-location` to read the device's position.

```bash
npx expo install expo-location
```

```jsx
import * as Location from 'expo-location';
import { useEffect, useState } from 'react';
import { Text, View } from 'react-native';

export default function Where() {
  const [coords, setCoords] = useState(null);
  const [error, setError] = useState(null);

  useEffect(() => {
    (async () => {
      const { status } = await Location.requestForegroundPermissionsAsync();
      if (status !== 'granted') {
        setError('Permission to access location was denied');
        return;
      }
      const loc = await Location.getCurrentPositionAsync({
        accuracy: Location.Accuracy.Balanced,
      });
      setCoords(loc.coords);
    })();
  }, []);

  if (error) return <Text>{error}</Text>;
  if (!coords) return <Text>Locating...</Text>;

  return (
    <View>
      <Text>Latitude: {coords.latitude}</Text>
      <Text>Longitude: {coords.longitude}</Text>
    </View>
  );
}
```

Related features:

- **Watch position** with `Location.watchPositionAsync` for live tracking.
- **Geocoding** with `Location.geocodeAsync` and `reverseGeocodeAsync` (address to coordinates and back).
- **Background location** needs extra permission and platform review. Only request it if your app truly needs it.
- **Maps** can be shown with `react-native-maps`.

Higher accuracy uses more battery, so choose the lowest accuracy that works for your feature.

---

## 10.3 Permissions

Sensitive features require the user's consent. Both platforms show a system dialog, and the user can deny it.

**Good permission practice**

1. **Ask in context** — request permission when the user taps a feature that needs it, not at app launch.
2. **Explain why** — show a short message of your own before the system dialog.
3. **Handle denial** — provide a fallback or a button that opens system settings.
4. **Check status first** — avoid asking again if it is already granted.

```jsx
import { Linking } from 'react-native';
import { useCameraPermissions } from 'expo-camera';

const [permission, requestPermission] = useCameraPermissions();

if (permission && !permission.granted && !permission.canAskAgain) {
  // The system will not show the dialog again; send the user to Settings
  return <Button title="Open Settings" onPress={() => Linking.openSettings()} />;
}
```

**Declare permissions and explanation text**

Many libraries are configured with **config plugins** in `app.json`. This sets the iOS usage descriptions (which Apple requires) and the Android manifest entries.

```json
{
  "expo": {
    "plugins": [
      [
        "expo-camera",
        {
          "cameraPermission": "Allow $(PRODUCT_NAME) to use your camera to scan receipts."
        }
      ],
      [
        "expo-location",
        {
          "locationWhenInUsePermission": "Allow $(PRODUCT_NAME) to show nearby stores."
        }
      ]
    ]
  }
}
```

Apple may reject apps with vague or missing permission explanations, so write clear, honest text. Changing plugin settings requires rebuilding the native app, so it will not take effect through a simple reload.

---

## 10.4 Push notifications

There are two kinds of notifications:

- **Local notifications** — scheduled by the app itself (reminders, timers).
- **Push notifications** — sent from a server to a device, even when the app is closed.

```bash
npx expo install expo-notifications expo-device
```

**Show notifications while the app is open**

```jsx
import * as Notifications from 'expo-notifications';

Notifications.setNotificationHandler({
  handleNotification: async () => ({
    shouldShowAlert: true,
    shouldPlaySound: false,
    shouldSetBadge: false,
  }),
});
```

In newer SDK versions, the handler's returned fields may be named `shouldShowBanner` and `shouldShowList` instead of `shouldShowAlert`. Check the current docs for your SDK version.

**Schedule a local notification**

```jsx
await Notifications.scheduleNotificationAsync({
  content: {
    title: 'Reminder',
    body: 'Time to stretch!',
  },
  trigger: { type: Notifications.SchedulableTriggerInputTypes.TIME_INTERVAL, seconds: 5 },
});
```

**Get a push token**

```jsx
import * as Device from 'expo-device';
import Constants from 'expo-constants';

async function registerForPush() {
  if (!Device.isDevice) {
    alert('Push notifications need a physical device');
    return;
  }

  const { status } = await Notifications.requestPermissionsAsync();
  if (status !== 'granted') return;

  const projectId = Constants.expoConfig?.extra?.eas?.projectId;
  const token = (await Notifications.getExpoPushTokenAsync({ projectId })).data;
  console.log('Push token:', token);
  // Send this token to your server and store it with the user
}
```

**Send a push**

Your server sends a request to Expo's push service, which forwards it to Apple (APNs) or Google (FCM):

```bash
curl -H "Content-Type: application/json" -X POST https://exp.host/--/api/v2/push/send \
  -d '{"to": "ExponentPushToken[xxxx]", "title": "Hello", "body": "From the server"}'
```

Things to know:

- Push needs a **physical device**. Simulators generally cannot receive remote pushes (iOS simulators have limited support).
- Android requires an FCM credential, and iOS requires Apple push credentials. EAS can help manage them.
- Remote push support in Expo Go is limited, so use a **development build** to test real push behavior.
- Handle taps with `Notifications.addNotificationResponseReceivedListener` to open a specific screen.

---

[Previous](./[9]-State-Management.md) | [Table of Contents](./[0]-Introduction-to-ReactNative.md) | [Next](./[11]-Animations-And-Gestures.md)