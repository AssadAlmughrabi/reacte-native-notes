# Project Folder Structure (React Native CLI)

## 1. Default structure

What you get after `npx @react-native-community/cli@latest init MyApp`:

```text
MyApp/
├── android/            # Native Android project (Gradle)
├── ios/                # Native iOS project (Xcode)
├── node_modules/       # Installed packages (do not edit, not committed)
├── __tests__/          # Jest tests
├── App.tsx             # Main app component (start here)
├── index.js            # Entry point, registers the app
├── app.json            # App name used by native code
├── package.json        # Dependencies and scripts
├── babel.config.js     # Babel config (JS transpiling)
├── metro.config.js     # Metro bundler config
├── tsconfig.json       # TypeScript config
├── jest.config.js      # Test config
├── Gemfile             # Ruby gems for iOS (CocoaPods)
├── .eslintrc.js        # Lint rules
└── .prettierrc.js      # Code formatting rules
```

---

## 2. Important files

| File | What it does |
| --- | --- |
| `index.js` | First file that runs. Registers `App` with `AppRegistry.registerComponent`. |
| `App.tsx` | Your root component. Everything starts from here. |
| `package.json` | Lists packages and scripts (`npm start`, `npm run android`, `npm run ios`). |
| `app.json` | Holds the app `name`. Must match the name in native code. |
| `metro.config.js` | Configures Metro, the bundler that serves your JS. |
| `babel.config.js` | Turns modern JS/JSX into code the JS engine understands. |

`index.js` looks like this:

```js
import { AppRegistry } from 'react-native';
import App from './App';
import { name as appName } from './app.json';

AppRegistry.registerComponent(appName, () => App);
```

---

## 3. Native folders

### android/

```text
android/
├── app/
│   ├── build.gradle                # applicationId, versionCode, versionName
│   └── src/main/
│       ├── AndroidManifest.xml     # Permissions, app settings
│       ├── java/com/myapp/         # MainActivity, MainApplication
│       └── res/                    # Icons, app name, splash
├── build.gradle                    # Project-level Gradle config
└── gradle.properties               # Gradle settings (New Architecture flag, etc.)
```

### ios/

```text
ios/
├── MyApp/
│   ├── AppDelegate.swift           # App startup
│   ├── Info.plist                  # Permissions, app settings
│   └── Images.xcassets/            # App icons
├── MyApp.xcodeproj/                # Xcode project
├── MyApp.xcworkspace/              # Open THIS in Xcode (after pod install)
└── Podfile                         # iOS native dependencies (CocoaPods)
```

You edit these when you need **permissions**, **app icons**, **app name/ID**, or **native library setup**.

---

## 4. Recommended `src/` structure

The default project puts everything in `App.tsx`. As the app grows, create a `src/` folder:

```text
MyApp/
├── src/
│   ├── components/     # Reusable UI (Button, Card, Input)
│   ├── screens/        # Full screens (HomeScreen, ProfileScreen)
│   ├── navigation/     # React Navigation setup (stacks, tabs)
│   ├── hooks/          # Custom hooks (useAuth, useFetch)
│   ├── services/       # API calls (api.ts, authService.ts)
│   ├── store/          # State management (Redux, Zustand)
│   ├── utils/          # Helper functions (formatDate, validators)
│   ├── constants/      # Colors, sizes, config values
│   ├── types/          # TypeScript types
│   └── assets/         # Images, fonts, icons
├── App.tsx             # Only sets up providers + navigation
└── index.js
```

| Folder | Put here |
| --- | --- |
| `components/` | Small pieces used on many screens |
| `screens/` | One file (or folder) per screen |
| `navigation/` | Navigators and route types |
| `services/` | Code that talks to the backend |
| `hooks/` | Logic you reuse across components |
| `assets/` | Static files: images, fonts |

---

## 5. Not committed to git

```text
node_modules/
ios/Pods/
android/build/
android/app/build/
ios/build/
```

These are generated. Recreate them with `npm install` and `cd ios && pod install`.

---

## Summary

- **`index.js`** registers the app, **`App.tsx`** is the root component.
- **`android/`** and **`ios/`** are real native projects that you own and edit.
- Put your own code in **`src/`**, split into `components`, `screens`, `navigation`, `services`, and so on.
- Never edit `node_modules/` and do not commit build folders.
