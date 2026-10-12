# Platform-Specific Code

Most React Native code runs the same on iOS and Android. When something must differ (look, behavior, APIs), you have three options:

| Option | Use when |
| --- | --- |
| `Platform.OS` | Small differences: one value or one line |
| `Platform.select` | Picking between several values, often styles |
| `.ios.js` / `.android.js` files | Big differences: a whole component |

---

## 1. Platform.OS

A string that tells you which platform the app is running on: `'ios'`, `'android'` or `'web'`.

```jsx
import { Platform, Text } from 'react-native';

function Hello() {
  return <Text>Running on {Platform.OS}</Text>;
}
```

### In logic

```jsx
if (Platform.OS === 'android') {
  // Android-only code
}
```

### In styles

```jsx
const styles = StyleSheet.create({
  header: {
    paddingTop: Platform.OS === 'ios' ? 44 : 0,
  },
});
```

### Common real example: KeyboardAvoidingView

```jsx
<KeyboardAvoidingView
  behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
  style={{ flex: 1 }}
>
```

---

## 2. Platform.select

Pass an object with a key per platform. It returns the value for the current one.

```jsx
const styles = StyleSheet.create({
  card: {
    backgroundColor: 'white',
    ...Platform.select({
      ios: {
        shadowColor: '#000',
        shadowOffset: { width: 0, height: 2 },
        shadowOpacity: 0.2,
        shadowRadius: 4,
      },
      android: {
        elevation: 4,
      },
    }),
  },
});
```

Shadows are a classic case: iOS uses `shadow*` properties, Android uses `elevation`.

Use `default` as a fallback:

```jsx
const fontFamily = Platform.select({
  ios: 'Helvetica',
  android: 'Roboto',
  default: 'System',
});
```

---

## 3. Platform-specific files

For big differences, make two files with the same name and a platform extension:

```text
Button.ios.tsx
Button.android.tsx
```

Import without the extension:

```jsx
import Button from './Button';
```

**Metro** (the bundler) automatically picks the right file for each platform.

Also possible: `Button.native.tsx` (iOS + Android) and `Button.tsx` (web).

---

## 4. Platform.Version

The OS version, if you need it:

- **Android:** API level number, like `34`
- **iOS:** version string, like `'17.4'`

```jsx
if (Platform.OS === 'android' && Platform.Version >= 33) {
  // Android 13+ only (for example, notification permission)
}
```

---

## Summary

- **`Platform.OS`** = `'ios'` or `'android'`. Use for small checks.
- **`Platform.select({ ios, android })`** = pick a value per platform. Great for styles.
- **`File.ios.tsx` / `File.android.tsx`** = separate files for big differences. Metro picks the right one.
- Keep platform code **small**. Most of your app should be shared.
