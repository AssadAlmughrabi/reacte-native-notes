# Automatic Token Refresh

**Automatic token refresh** gets a new access token **in the background** when the old one expires. The user never notices and never has to log in again (until the refresh token expires).

---

## Two ways to do it

| Way | When it refreshes | Notes |
| --- | --- | --- |
| **Reactive** (most common) | **After** the server returns `401` | Simple, always correct |
| **Proactive** | **Before** the request, if the token is about to expire | Saves one failed request |

Many apps use **reactive** only. Some use both.

---

## Reactive: refresh on 401

Done in an Axios **response interceptor**:

```ts
let refreshPromise: Promise<string> | null = null;

api.interceptors.response.use(
  res => res,
  async error => {
    const original = error.config;

    if (error.response?.status !== 401 || original._retry) {
      return Promise.reject(error);
    }
    original._retry = true;

    try {
      refreshPromise ??= refreshAccessToken();   // one refresh for many requests
      const newToken = await refreshPromise;
      original.headers.Authorization = `Bearer ${newToken}`;
      return api(original);                      // retry
    } catch (e) {
      await logout();                            // refresh failed
      return Promise.reject(e);
    } finally {
      refreshPromise = null;
    }
  }
);
```

---

## Proactive: refresh before it expires

Read the expiry time (`exp`) from the JWT and refresh early in a **request interceptor**:

```ts
import { jwtDecode } from 'jwt-decode';

function isExpiringSoon(token: string) {
  const { exp } = jwtDecode<{ exp: number }>(token);
  return exp * 1000 - Date.now() < 60_000;   // less than 1 minute left
}

api.interceptors.request.use(async config => {
  let token = await getAccessToken();

  if (token && isExpiringSoon(token)) {
    try {
      refreshPromise ??= refreshAccessToken();
      token = await refreshPromise;
    } finally {
      refreshPromise = null;
    }
  }

  if (token) config.headers.Authorization = `Bearer ${token}`;
  return config;
});
```

```bash
npm install jwt-decode
```

---

## The refresh function

```ts
async function refreshAccessToken(): Promise<string> {
  const tokens = await loadTokens();                          // from Keychain
  const res = await axios.post(`${BASE_URL}/auth/refresh`, {  // plain axios, not api
    refreshToken: tokens?.refreshToken,
  });
  await saveTokens(res.data);                                 // save new tokens
  return res.data.accessToken;
}
```

---

## Must-haves

- **One refresh at a time:** share `refreshPromise`, so 5 failed requests make only 1 refresh call.
- **Retry once:** the `_retry` flag stops endless loops.
- **Plain `axios` for the refresh call:** so it does not pass through your own interceptor.
- **Refresh fails → logout:** clear tokens and go to the login screen.

---

## Summary

- Automatic refresh = new access token **without** the user noticing.
- **Reactive:** on `401`, refresh and retry (response interceptor).
- **Proactive:** refresh before `exp` (request interceptor + `jwt-decode`).
- One shared refresh call, retry once, logout if refresh fails.
