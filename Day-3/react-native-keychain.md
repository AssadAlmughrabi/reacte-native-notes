# react-native-keychain

**react-native-keychain** saves secrets in the phone's **secure storage**:

- **iOS:** Keychain
- **Android:** Keystore (encrypted)

Use it for: auth tokens, refresh tokens, passwords.

---

## Install

```bash
npm install react-native-keychain
cd ios && pod install && cd ..
```

Rebuild the app after installing (`npm run android` / `npm run ios`).

---

## Basic methods

It stores a **username + password** pair. You can put anything in those two strings.

```tsx
import * as Keychain from 'react-native-keychain';

// Save
await Keychain.setGenericPassword('user@email.com', 'my-token');

// Read (returns false if nothing saved)
const creds = await Keychain.getGenericPassword();
if (creds) {
  console.log(creds.username);   // 'user@email.com'
  console.log(creds.password);   // 'my-token'
}

// Delete
await Keychain.resetGenericPassword();
```

---

## Save more than one value

### Option 1: JSON in the password

```tsx
const tokens = { accessToken: 'abc', refreshToken: 'xyz' };

await Keychain.setGenericPassword('tokens', JSON.stringify(tokens));

const creds = await Keychain.getGenericPassword();
const saved = creds ? JSON.parse(creds.password) : null;
```

### Option 2: separate items with `service`

```tsx
await Keychain.setGenericPassword('auth', accessToken, { service: 'accessToken' });
await Keychain.setGenericPassword('auth', refreshToken, { service: 'refreshToken' });

const access = await Keychain.getGenericPassword({ service: 'accessToken' });

await Keychain.resetGenericPassword({ service: 'accessToken' });
```

---

## Example: token helper

```tsx
// src/services/tokenStorage.ts
import * as Keychain from 'react-native-keychain';

export async function saveToken(token: string) {
  await Keychain.setGenericPassword('auth', token);
}

export async function getToken(): Promise<string | null> {
  const creds = await Keychain.getGenericPassword();
  return creds ? creds.password : null;
}

export async function clearToken() {
  await Keychain.resetGenericPassword();
}
```

- **Login:** `saveToken(token)`
- **App start:** `getToken()` to check if the user is logged in
- **Logout:** `clearToken()`

---

## Protect with Face ID / fingerprint (optional)

```tsx
await Keychain.setGenericPassword('auth', token, {
  accessControl: Keychain.ACCESS_CONTROL.BIOMETRY_ANY,
  accessible: Keychain.ACCESSIBLE.WHEN_UNLOCKED_THIS_DEVICE_ONLY,
});

// Reading now asks for Face ID / fingerprint
const creds = await Keychain.getGenericPassword({
  authenticationPrompt: { title: 'Confirm to continue' },
});
```

iOS needs `NSFaceIDUsageDescription` in `Info.plist` (the message shown to the user).

---

## Things to know

- All methods are **async**: use `await` and `try/catch`.
- `getGenericPassword()` returns **`false`** (not `null`) when nothing is saved.
- **iOS:** Keychain data can **survive an app uninstall**. Clear it on first launch if needed.
- Keep it for **small secrets**. Normal data still goes in AsyncStorage.

---

## Summary

- Saves secrets in **Keychain (iOS) / Keystore (Android)**.
- `setGenericPassword`, `getGenericPassword`, `resetGenericPassword`.
- Use `service` or JSON to store several values.
- Save on login, read on app start, **clear on logout**.
