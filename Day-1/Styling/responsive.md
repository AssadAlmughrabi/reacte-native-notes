# Responsive Layout

Phones and tablets have different sizes, notches and orientations. There are no media queries in React Native, so you use these tools instead:

| Tool | Used for |
| --- | --- |
| `useWindowDimensions` | Getting the screen width and height |
| Safe area | Keeping content away from notches and system bars |
| Orientation | Handling portrait and landscape |

---

## 1. useWindowDimensions

A hook that returns the current window size. It **updates automatically** when the size changes (rotation, split screen).

```jsx
import { useWindowDimensions, View, Text } from 'react-native';

function Box() {
  const { width, height } = useWindowDimensions();

  return (
    <View style={{ width: width * 0.8, padding: 16 }}>
      <Text>Screen: {width} x {height}</Text>
    </View>
  );
}
```

### Replace media queries

```jsx
const { width } = useWindowDimensions();
const isTablet = width >= 768;

<View style={{ flexDirection: isTablet ? 'row' : 'column' }}>
```

### Grid columns based on width

```jsx
const { width } = useWindowDimensions();
const columns = width >= 768 ? 3 : 2;

<FlatList data={items} numColumns={columns} key={columns} ... />
```

(`key={columns}` is needed because `FlatList` cannot change `numColumns` on the fly.)

> **Avoid `Dimensions.get('window')`** inside components. It reads the size once and does **not** update on rotation. Use the hook instead.

---

## 2. Safe Area

The **safe area** is the part of the screen not covered by the notch, status bar, rounded corners or home indicator.

Use the **`react-native-safe-area-context`** library (Expo includes it). The built-in `SafeAreaView` from `react-native` only works on iOS and is deprecated.

```bash
npx expo install react-native-safe-area-context
# or
npm install react-native-safe-area-context
```

### Step 1: Wrap your app once

```jsx
import { SafeAreaProvider } from 'react-native-safe-area-context';

export default function App() {
  return (
    <SafeAreaProvider>
      <HomeScreen />
    </SafeAreaProvider>
  );
}
```

### Step 2: Use SafeAreaView in screens

```jsx
import { SafeAreaView } from 'react-native-safe-area-context';

function HomeScreen() {
  return (
    <SafeAreaView style={{ flex: 1 }}>
      <Text>Not hidden behind the notch</Text>
    </SafeAreaView>
  );
}
```

Pick only some edges:

```jsx
<SafeAreaView edges={['top']} style={{ flex: 1 }}>
```

### Or use the insets directly

`useSafeAreaInsets` gives you the exact space to leave on each side.

```jsx
import { useSafeAreaInsets } from 'react-native-safe-area-context';

function Header() {
  const insets = useSafeAreaInsets();

  return (
    <View style={{ paddingTop: insets.top, backgroundColor: 'blue' }}>
      <Text>Header</Text>
    </View>
  );
}
```

Useful when a background color should go **behind** the status bar, but the content should not.

---

## 3. Orientation

### Detect portrait or landscape

```jsx
const { width, height } = useWindowDimensions();
const isLandscape = width > height;

<View style={{ flexDirection: isLandscape ? 'row' : 'column' }}>
```

The component re-renders automatically when the device rotates.

### Lock orientation

**Expo:** in `app.json`

```json
{
  "expo": {
    "orientation": "portrait"
  }
}
```

Values: `"portrait"`, `"landscape"`, `"default"` (both).

To change it while the app runs, use `expo-screen-orientation`:

```jsx
import * as ScreenOrientation from 'expo-screen-orientation';

await ScreenOrientation.lockAsync(ScreenOrientation.OrientationLock.LANDSCAPE);
```

**React Native CLI:**

- Android: `android:screenOrientation="portrait"` on the activity in `AndroidManifest.xml`
- iOS: Xcode > target > General > Device Orientation

---

## Summary

- **`useWindowDimensions`** = screen size that updates on rotation. Use it instead of media queries.
- **Safe area** = use `react-native-safe-area-context`: `SafeAreaProvider` once, then `SafeAreaView` or `useSafeAreaInsets`.
- **Orientation** = `width > height` means landscape. Lock it in `app.json` (Expo) or the native config (CLI).
- Combine with **Flexbox** and **percentages** (`width: '50%'`) for layouts that fit any screen.
