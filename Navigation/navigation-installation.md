# Navigation Installation and Setup (React Native CLI)

React Navigation needs a few native libraries to work:

| Package | What it does |
| --- | --- |
| `@react-navigation/native` | Core of React Navigation |
| `react-native-screens` | Uses real native screens, faster and lighter |
| `react-native-safe-area-context` | Keeps content away from notches and system bars |
| `react-native-gesture-handler` | Native swipe and touch gestures (needed for drawer and the JS stack) |

---

## 1. Install the core

```bash
npm install @react-navigation/native
npm install react-native-screens react-native-safe-area-context
```

---

## 2. Native setup

### iOS: install pods

```bash
cd ios && pod install && cd ..
```

Run this every time you add a library with native code.

### Android: fix for react-native-screens

In `android/app/src/main/java/com/myapp/MainActivity.kt`, add:

```kotlin
import android.os.Bundle

class MainActivity : ReactActivity() {
  // ...

  override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(null)
  }
}
```

This prevents crashes when Android restores the app after it was killed in the background.

---

## 3. safe-area-context

Wrap your app in `SafeAreaProvider` once. `NavigationContainer` already handles safe areas for headers and tabs, but your own screens need it too.

```jsx
import { SafeAreaProvider } from 'react-native-safe-area-context';

<SafeAreaProvider>
  {/* app */}
</SafeAreaProvider>
```

In screens without a header, use `SafeAreaView` or `useSafeAreaInsets` from `react-native-safe-area-context` (not from `react-native`).

---

## 4. gesture-handler

Only needed for **drawer navigation** or the JS **stack** (`@react-navigation/stack`). The **native stack** (`@react-navigation/native-stack`) and **bottom tabs** do not need it.

### Install

```bash
npm install react-native-gesture-handler
cd ios && pod install && cd ..
```

### Wrap the app

Wrap the root of your app in `GestureHandlerRootView`:

```jsx
import { GestureHandlerRootView } from 'react-native-gesture-handler';

<GestureHandlerRootView style={{ flex: 1 }}>
  {/* app */}
</GestureHandlerRootView>
```

> Drawer navigation also needs **`react-native-reanimated`**. Install it and follow its setup (Babel plugin) if you use a drawer.

---

## 5. Put it all together

Install a navigator, for example the native stack:

```bash
npm install @react-navigation/native-stack
```

`App.tsx`:

```jsx
import { GestureHandlerRootView } from 'react-native-gesture-handler';
import { SafeAreaProvider } from 'react-native-safe-area-context';
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';
import HomeScreen from './src/screens/HomeScreen';
import DetailsScreen from './src/screens/DetailsScreen';

const Stack = createNativeStackNavigator();

export default function App() {
  return (
    <GestureHandlerRootView style={{ flex: 1 }}>
      <SafeAreaProvider>
        <NavigationContainer>
          <Stack.Navigator>
            <Stack.Screen name="Home" component={HomeScreen} />
            <Stack.Screen name="Details" component={DetailsScreen} />
          </Stack.Navigator>
        </NavigationContainer>
      </SafeAreaProvider>
    </GestureHandlerRootView>
  );
}
```

Order from outside to inside: **GestureHandlerRootView**, then **SafeAreaProvider**, then **NavigationContainer**.

---

## 6. Rebuild the app

New native code needs a new build. Fast Refresh is not enough.

```bash
npm run android
npm run ios
```

If something breaks, clear the cache: `npm start -- --reset-cache`.

---

## Summary

- Install **`@react-navigation/native`**, **`react-native-screens`** and **`react-native-safe-area-context`**.
- Run `pod install` for iOS and add the `onCreate` fix in `MainActivity` for Android.
- **Gesture handler:** only for drawer or JS stack. Wrap the app in `GestureHandlerRootView`.
- Wrap order: `GestureHandlerRootView`, then `SafeAreaProvider`, then `NavigationContainer`.
- **Rebuild** the app after installing native libraries.
