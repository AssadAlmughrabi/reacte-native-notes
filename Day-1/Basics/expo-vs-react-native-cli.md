# Expo vs React Native CLI

## In one sentence

Both create and run React Native apps with the **same code**. The difference is **how much native setup you do yourself**.

- **React Native CLI:** you set up and manage everything yourself.
- **Expo:** a framework on top of React Native that does most of the setup for you (like Next.js on top of React).

## 1. React Native CLI

```bash
npx @react-native-community/cli init MyApp
cd MyApp
npx react-native run-android   # or run-ios
```

How it works:

- Your project has real **`android/`** and **`ios/`** folders. You commit them and you maintain them.
- **Metro** serves your JavaScript. **Gradle** (Android) or **Xcode** (iOS) builds the native app on your computer.
- You need **Android Studio + Android SDK**. For iOS you need **a Mac with Xcode + CocoaPods**.
- Native libraries link automatically (**autolinking**), but sometimes you still edit native files by hand (`AndroidManifest.xml`, `Info.plist`, `Podfile`).
- To release, you make signed builds yourself and upload them to the stores.

**Good:** full control.
**Bad:** you handle all native setup and every upgrade yourself, and upgrades can be painful.

## 2. Expo

```bash
npx create-expo-app@latest MyApp
cd MyApp
npx expo start
```

How it works:

- It is still React Native and still uses Metro. Expo adds **tools, ready-made native modules, and cloud services**.
- **Expo SDK modules:** tested packages for camera, notifications, files, location and more (`expo-camera`, `expo-notifications`).
- **Expo Router:** file-based navigation, like Next.js.
- **No native folders to maintain:** Expo generates `android/` and `ios/` from `app.json` with `npx expo prebuild`. Settings like app icon, permissions and bundle ID go in that config file. **Config plugins** apply native changes that libraries need.
- **Upgrading:** mostly change the SDK version and regenerate.

### Two ways to run your app while developing

| Option | What it is | Use it for |
| --- | --- | --- |
| **Expo Go** | An app from the store. Scan a QR code and your app runs inside it. Only has Expo's built-in modules, so **no custom native code**. | Learning and quick prototypes |
| **Development build** | Your own debug version of the app, with any native libraries you added. | Real projects |

### EAS (Expo Application Services), optional cloud tools

- **EAS Build:** builds your app in the cloud, so you can build for iOS **without a Mac**.
- **EAS Submit:** uploads your app to the App Store and Play Store.
- **EAS Update:** sends JavaScript fixes to users right away, without a store release.

## 3. Side by side

| Topic | React Native CLI | Expo |
| --- | --- | --- |
| Getting started | Install Xcode, Android Studio, SDKs first | Create the app and run it right away |
| Native folders | You commit and maintain them | Generated from config |
| Native settings | Edit native files directly | `app.json` + config plugins |
| Native libraries | Any | Any (with a development build) |
| Navigation | Add React Navigation yourself | Expo Router built in |
| Build for iOS | Needs a Mac | Mac, or EAS Build in the cloud |
| Over-the-air updates | Set up a third-party tool | EAS Update |
| Upgrades | Manual, often painful | Change SDK version, regenerate |
| Control | Total | Very high, and you can still write native code |

## 4. Common myth

> "Expo can't use native code, so serious apps need the CLI."

**This is outdated.** With development builds, config plugins and Expo Modules (for writing your own Swift/Kotlin code), Expo apps can use **any** native library. Only **Expo Go** is limited.

**When the CLI still makes sense:**

- Adding React Native to an existing native iOS/Android app
- Teams with lots of custom native code and their own native build setup

## 5. Short answer to say out loud

> "Both run the same React Native code with Metro. With the React Native CLI, you own the iOS and Android projects, so you need Xcode and Android Studio and you handle native setup and upgrades yourself. Expo is a framework on top of React Native: it generates the native projects from a config file, gives you tested native modules and file-based routing, and offers cloud builds, store uploads and over-the-air updates through EAS. Expo Go is only for quick prototypes; real Expo apps use development builds, which support any native library. Expo is now the officially recommended way to start."
