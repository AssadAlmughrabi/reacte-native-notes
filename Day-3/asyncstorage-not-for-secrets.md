# Why AsyncStorage Is Not for Secrets

## What counts as a secret?

- Auth tokens (access token, refresh token)
- Passwords and PIN codes
- Private keys, API secrets
- Personal sensitive data (ID numbers, card details)

---

## The problem

AsyncStorage saves data as **plain text**, with **no encryption**:

- **Android:** a SQLite database file in the app's data folder.
- **iOS:** normal files in the app's folder.

The OS keeps other apps out **normally**, but that protection breaks easily:

| Risk | How the secret leaks |
| --- | --- |
| **Rooted / jailbroken phone** | Root access can open any app's files |
| **Device backups** | Files are copied into phone or computer backups |
| **Malware** | Malicious apps with elevated access read the files |
| **Lost or stolen phone** | Files pulled with debugging tools |

A leaked token lets someone **act as the user** without knowing their password.

---

## The safe place: Keychain / Keystore

Phones have a built-in **secure storage**:

- **iOS:** Keychain
- **Android:** Keystore

| | AsyncStorage | Keychain / Keystore |
| --- | --- | --- |
| Encrypted | No | **Yes**, by the OS |
| Keys kept in secure hardware | No | **Yes** (on most phones) |
| Can require Face ID / fingerprint | No | **Yes** |
| Use for | Theme, language, settings | **Tokens, passwords, secrets** |

Use it from React Native with **`react-native-keychain`**:

```bash
npm install react-native-keychain
cd ios && pod install && cd ..
```

```tsx
import * as Keychain from 'react-native-keychain';

await Keychain.setGenericPassword('token', accessToken);   // save

const creds = await Keychain.getGenericPassword();          // read
const token = creds ? creds.password : null;

await Keychain.resetGenericPassword();                     // delete on logout
```

---

## One more rule: no secrets in the app code

Anything inside your JS bundle (like an API secret key in a `.env` file) **can be extracted** from the app. Keep real secrets on your **server**, never in the app.

---

## Summary

- AsyncStorage = **plain text**, readable on rooted phones, backups, malware.
- Secrets go in **Keychain (iOS) / Keystore (Android)** via `react-native-keychain`.
- Delete tokens on **logout**.
- Never put API secrets in the app code. Keep them on the server.
