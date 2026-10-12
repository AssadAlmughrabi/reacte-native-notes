# Setup and Run React Native (Linux + Android)

## 1. Install requirements

| Tool | Version | Check |
| --- | --- | --- |
| Node.js | 20+ | `node -v` |
| Java JDK | 17 | `java -version` |
| Android Studio + SDK | latest | `echo $ANDROID_HOME` |

Install JDK 17 (if missing):

```bash
sudo apt install openjdk-17-jdk
```

Install Android Studio from <https://developer.android.com/studio>, then open it and install:

- Android SDK Platform (latest)
- Android SDK Build-Tools
- Android SDK Platform-Tools
- Android Emulator

## 2. Set environment variables

Add to `~/.bashrc`:

```bash
export ANDROID_HOME=$HOME/Android/Sdk
export PATH=$PATH:$ANDROID_HOME/emulator
export PATH=$PATH:$ANDROID_HOME/platform-tools
```

Reload:

```bash
source ~/.bashrc
```

Check:

```bash
adb --version
```

## 3. Create a new project

```bash
npx @react-native-community/cli@latest init MyApp
cd MyApp
```

## 4. Run the app

### Step 1: Start the emulator

```bash
emulator -avd <your-emulator-name> ||  Medium_Phone_API_37.0
```

A phone window opens. Wait until it reaches the home screen, which takes about a minute.

> To see your emulator names, run `emulator -list-avds` (example: `Medium_Phone_API_37.0`). To create one, use Android Studio > Device Manager.

### Step 2: Start Metro in a second terminal

```bash
npm start
```

Leave this terminal open. It serves your JavaScript to the app.

### Step 3: Install and launch the app in a third terminal

```bash
npm run android
```

When it prints `BUILD SUCCESSFUL`, your app opens in the emulator by itself.

### Step 4: Reopen the app later without rebuilding

Either:

- swipe up on the emulator's home screen and tap your app's icon, or
- run:

  ```bash
  adb shell am start -n com.myapp/.MainActivity
  ```

  Replace `com.myapp` with your app's package name (the `applicationId` in `android/app/build.gradle`).

### Step 5: Make changes

Edit `App.tsx` and save, and the emulator updates automatically. To force a reload, press `r` in the Metro terminal.

## Common problems

| Problem | Fix |
| --- | --- |
| `SDK location not found` | Check `ANDROID_HOME` is set, or create `android/local.properties` with `sdk.dir=/home/<user>/Android/Sdk` |
| `No connected devices` | Start the emulator or run `adb devices` |
| Build fails after changes | `cd android && ./gradlew clean && cd ..` then `npm run android` |
| Metro cache issues | `npm start -- --reset-cache` |
| Check whole setup | `npx react-native doctor` |
