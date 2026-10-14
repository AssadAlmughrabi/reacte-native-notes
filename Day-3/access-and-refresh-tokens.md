# Access and Refresh Tokens: Where to Store Them

## The two tokens

| | Access token | Refresh token |
| --- | --- | --- |
| Job | Sent with every API request to prove who you are | Gets a **new access token** when the old one expires |
| Lifetime | **Short** (minutes, e.g. 15 min) | **Long** (days or weeks) |
| Sent | On every request: `Authorization: Bearer <token>` | Only to the refresh endpoint |
| If stolen | Works only until it expires | Can create new access tokens for a long time |

The refresh token is the **more dangerous** one to leak.

---

## Where to store them

| Token | Store in | Why |
| --- | --- | --- |
| **Access token** | **Memory** (state / Zustand) + Keychain | Fast to read on every request. Keychain so the user stays logged in after restart |
| **Refresh token** | **Keychain only** | Long-lived and powerful, so it needs the safest place |
| Either one | **Never AsyncStorage** | Plain text, not encrypted |

> Some apps keep only the **refresh token** in Keychain and get a fresh access token on app start. That is even safer.

---

## The flow

```text
1. Login         → server returns accessToken + refreshToken
2. Save          → both in Keychain, accessToken also in memory
3. API request   → send accessToken in the header
4. 401 error     → access token expired
5. Refresh       → send refreshToken, get a new accessToken (and often a new refreshToken)
6. Save again    → update Keychain + memory, retry the request
7. Refresh fails → log the user out
8. Logout        → delete both from Keychain and memory
```

---

## Example: save and load

```tsx
import * as Keychain from 'react-native-keychain';

type Tokens = { accessToken: string; refreshToken: string };

export async function saveTokens(tokens: Tokens) {
  await Keychain.setGenericPassword('auth', JSON.stringify(tokens));
}

export async function loadTokens(): Promise<Tokens | null> {
  const creds = await Keychain.getGenericPassword();
  return creds ? JSON.parse(creds.password) : null;
}

export async function clearTokens() {
  await Keychain.resetGenericPassword();
}
```

On app start:

```tsx
const tokens = await loadTokens();
if (tokens) {
  setAccessToken(tokens.accessToken);   // into memory (state / Zustand)
  // user is logged in
}
```

---

## Rules

- **Never** store tokens in AsyncStorage or log them with `console.log`.
- Keep the access token **short-lived**.
- **Delete both** tokens on logout.
- If the refresh request fails, **log the user out**.
- Always use **HTTPS** so tokens are not readable on the network.

---

## Summary

- **Access token:** short-lived, used on every request. Keep in **memory** (+ Keychain).
- **Refresh token:** long-lived, gets new access tokens. **Keychain only.**
- Never AsyncStorage.
- On `401`: refresh, retry. If refresh fails: logout and clear everything.
