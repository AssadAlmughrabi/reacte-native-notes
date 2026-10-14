# Biometric Authentication

**Biometric authentication** lets the user unlock the app with **Face ID, Touch ID or fingerprint** instead of typing a password.

Common uses: unlock the app on open, confirm a payment, log in with a saved token.

---

## Two ways

| Way | Library | Security |
| --- | --- | --- |
| **Simple prompt** | `react-native-biometrics` | Just a yes/no check in the app |
| **Protect the token itself** | `react-native-keychain` | The OS only gives the token **after** a successful scan |

The second way is **safer**: the secret cannot be read without the biometric check.

---

## Setup

**iOS** (`ios/MyApp/Info.plist`), required for Face ID:

```xml
<key>NSFaceIDUsageDescription</key>
<string>Use Face ID to log in securely</string>
```

**Android** (`AndroidManifest.xml`):

```xml
<uses-permission android:name="android.permission.USE_BIOMETRIC" />
```

---

## Way 1: Simple prompt

```bash
npm install react-native-biometrics
cd ios && pod install && cd ..
```

```ts
import ReactNativeBiometrics from 'react-native-biometrics';

const rnBiometrics = new ReactNativeBiometrics();

// 1. Check if the device supports it
const { available, biometryType } = await rnBiometrics.isSensorAvailable();
// biometryType: 'FaceID' | 'TouchID' | 'Biometrics'

// 2. Show the prompt
if (available) {
  const { success } = await rnBiometrics.simplePrompt({ promptMessage: 'Confirm your identity' });

  if (success) {
    // unlock the app
  }
}
```

> This only checks **in the app**. On a hacked device, the check can be skipped. Fine for unlocking the UI, not for protecting secrets.

---

## Way 2: Token protected by biometrics (safer)

Save the token so it can **only** be read after Face ID / fingerprint:

```ts
import * as Keychain from 'react-native-keychain';

// After login: save with biometric protection
await Keychain.setGenericPassword('auth', refreshToken, {
  accessControl: Keychain.ACCESS_CONTROL.BIOMETRY_ANY,
  accessible: Keychain.ACCESSIBLE.WHEN_PASSCODE_SET_THIS_DEVICE_ONLY,
});

// Next app open: reading shows the biometric prompt
const creds = await Keychain.getGenericPassword({
  authenticationPrompt: { title: 'Log in with Face ID' },
});

if (creds) {
  const token = creds.password;   // scan worked: use the token
}
```

---

## Good practices

- Always offer a **fallback**: password or PIN, in case biometrics fail or are not set up.
- Ask the user first: "**Enable Face ID login?**" Do not turn it on silently.
- Check `isSensorAvailable()` before showing the option.
- Handle **cancel**: the user may close the prompt.

---

## Summary

- Biometrics = Face ID / Touch ID / fingerprint.
- **Simple prompt** (`react-native-biometrics`): easy, but only an in-app check.
- **Keychain with `accessControl`**: the token is locked behind the scan. Safer.
- Add `NSFaceIDUsageDescription` (iOS) and `USE_BIOMETRIC` (Android).
- Always offer a password/PIN fallback.
