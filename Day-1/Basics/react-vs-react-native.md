# React vs React Native

## In one sentence

**React** builds UI out of components. **React Native** uses the same React to build real mobile apps instead of web pages.

## 1. Same React, different output

React itself (components, JSX, props, state, hooks) does not care where it runs. A **renderer** decides what appears on screen:

| Renderer | Turns components into |
| --- | --- |
| **React DOM** (web) | HTML elements like `<div>` and `<button>`, styled with CSS |
| **React Native** (mobile) | Real native views: a `UIView` on iOS, an `android.view.View` on Android |

React Native is **not** a website inside an app. It draws real native screens.

That is why `useState`, `useEffect`, props, context and custom hooks work the same in both.

## 2. Main differences

| Topic | React (web) | React Native (mobile) |
| --- | --- | --- |
| Runs on | Browser | iOS and Android |
| Building blocks | `div`, `span`, `img`, `input`, `button` | `View`, `Text`, `Image`, `TextInput`, `Pressable` |
| Styling | CSS files and class names | JS objects with `StyleSheet.create` |
| Layout | Flexbox is optional | Always Flexbox, default direction is **column** |
| Units | px, rem, %, vh | Plain numbers (no units) |
| Navigation | URLs (React Router) | Screen stacks and tabs (React Navigation, Expo Router) |
| Device features | Browser APIs (`localStorage`, `fetch`) | Native modules (camera, notifications, AsyncStorage) |
| Shipping | Deploy to a server, users get it right away | Build an app and submit it to the App Store / Play Store |
| Debugging | Browser DevTools | React Native DevTools, Metro, emulator |

## 3. Same component, both ways

```jsx
// React (web)
<div className="card">
  <h2>Hello</h2>
  <button onClick={save}>Save</button>
</div>

// React Native
<View style={styles.card}>
  <Text style={styles.title}>Hello</Text>
  <Pressable onPress={save}>
    <Text>Save</Text>
  </Pressable>
</View>
```

What to notice:

- **All text must be inside `<Text>`.** Plain text directly inside a `<View>` causes an error.
- **`onClick` becomes `onPress`**, because phones use touch, not a mouse.
- **Styles do not pass down.** A `<Text>` does not get font styles from its parent `<View>`. Only a `<Text>` inside another `<Text>` inherits them.

## 4. Things only React Native cares about

- **Long lists:** use `FlatList`, which only draws the rows on screen. Using `.map()` for 1,000 rows makes the app slow.
- **iOS vs Android:** check `Platform.OS === 'ios'`, or make separate files like `Button.ios.tsx` and `Button.android.tsx`.
- **Threads:** your JavaScript runs separately from the native UI. The **New Architecture** (Fabric, JSI, TurboModules, now the default) lets JS call native code directly, instead of sending messages through the old slow "bridge". This comes up a lot in interviews.
- **Updates:** native code changes need a new store release. JS-only changes can be sent over the air with tools like Expo EAS Update.
- **Expo:** the recommended way to start a new app. It handles builds and native setup, so you rarely open Xcode or Android Studio.

## 5. What you can share between web and mobile

These work in both:

- Business logic and custom hooks
- State management (Redux, Zustand, React Query)
- API code (fetch, Axios)
- Validation
- TypeScript types

The **UI** must be rewritten, unless you use `react-native-web` to run React Native components in a browser.

## 6. Short answer to say out loud

> "React is a UI library. React DOM shows it in the browser with HTML and CSS. React Native uses the same React but shows real native iOS and Android screens. Hooks and state work the same, but the building blocks are different (`View` and `Text` instead of `div` and `span`), styles are JavaScript objects with Flexbox, navigation uses screen stacks instead of URLs, device features come from native modules, and apps ship through app stores instead of a server."
