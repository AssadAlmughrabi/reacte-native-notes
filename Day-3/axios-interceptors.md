# Axios Interceptors

**Interceptors** are functions that run **automatically** on every request or response. Write the logic once, and every API call uses it.

| Interceptor | Runs | Common use |
| --- | --- | --- |
| **Request** | Before every request is sent | Add the auth token to headers |
| **Response** | After every response comes back | Handle errors, refresh expired tokens |

---

## Install

```bash
npm install axios
```

---

## 1. Create an API instance

```ts
// src/services/api.ts
import axios from 'axios';

export const api = axios.create({
  baseURL: 'https://api.example.com',
  timeout: 10000,
});
```

Use `api` everywhere instead of plain `axios`.

---

## 2. Request interceptor: add the token

```ts
api.interceptors.request.use(async config => {
  const token = await getAccessToken();   // from memory or Keychain
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});
```

Now every call sends the token automatically:

```ts
const res = await api.get('/profile');   // header added for you
```

---

## 3. Response interceptor: refresh on 401

When the access token expires, the server returns **401**. The interceptor gets a new token and **retries** the request.

```ts
let refreshPromise: Promise<string> | null = null;

api.interceptors.response.use(
  response => response,                       // success: pass through
  async error => {
    const original = error.config;

    if (error.response?.status === 401 && !original._retry) {
      original._retry = true;                 // retry only once

      try {
        // Share one refresh call if many requests fail at the same time
        refreshPromise ??= refreshAccessToken();
        const newToken = await refreshPromise;
        refreshPromise = null;

        original.headers.Authorization = `Bearer ${newToken}`;
        return api(original);                 // retry with the new token
      } catch (refreshError) {
        refreshPromise = null;
        await logout();                       // refresh failed: clear tokens, go to login
        return Promise.reject(refreshError);
      }
    }

    return Promise.reject(error);
  }
);
```

```ts
async function refreshAccessToken(): Promise<string> {
  const tokens = await loadTokens();          // from Keychain
  const res = await axios.post('https://api.example.com/auth/refresh', {
    refreshToken: tokens?.refreshToken,
  });
  await saveTokens(res.data);                 // save new tokens
  return res.data.accessToken;
}
```

Key points:

- **`_retry` flag:** stops an endless loop if the retry also fails.
- **`refreshPromise`:** if 5 requests fail together, only **one** refresh call is made.
- Use plain **`axios`** (not `api`) for the refresh call, so it does not go through the interceptor.

---

## The flow

```text
api.get('/profile')
  → request interceptor adds token
  → server: 401 (token expired)
  → response interceptor refreshes token
  → retries request with new token
  → success
```

---

## Summary

- **Request interceptor:** add `Authorization` header to every call.
- **Response interceptor:** on `401`, refresh the token and retry once.
- Use one **`api` instance** for all calls.
- If refresh fails: **logout** and clear tokens.
