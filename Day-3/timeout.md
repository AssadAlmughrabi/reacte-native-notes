# Timeout

A **timeout** is the maximum time to wait for a server response. If the server takes longer, the request is **cancelled** and treated as an error.

Without a timeout, a slow or dead server can leave the user staring at a **spinner forever**.

---

## Axios

Set it once on the instance (in milliseconds):

```ts
export const api = axios.create({
  baseURL: 'https://api.example.com',
  timeout: 10000,   // 10 seconds
});
```

Override for one slow request (like an upload):

```ts
await api.post('/upload', formData, { timeout: 60000 });   // 60 seconds
```

> The default is `0`, which means **no timeout**. Always set one.

### Detect a timeout

```ts
try {
  await api.get('/orders');
} catch (error) {
  if (axios.isAxiosError(error) && error.code === 'ECONNABORTED') {
    showMessage('The server is taking too long. Please try again.');
  }
}
```

---

## fetch

`fetch` has **no timeout option**. Use an `AbortController`:

```ts
async function fetchWithTimeout(url: string, ms = 10000) {
  const controller = new AbortController();
  const timer = setTimeout(() => controller.abort(), ms);

  try {
    return await fetch(url, { signal: controller.signal });
  } finally {
    clearTimeout(timer);
  }
}
```

A timeout throws an error with `name === 'AbortError'`.

---

## Good values

| Request | Timeout |
| --- | --- |
| Normal API calls | 10 to 15 seconds |
| Login / small forms | 10 seconds |
| File upload / download | 30 to 120 seconds |

Mobile networks are slow sometimes, so do not go too short.

---

## Summary

- Timeout = stop waiting after X ms and throw an error.
- **Axios:** `timeout` on the instance. Default `0` = never times out.
- **fetch:** use `AbortController` + `setTimeout`.
- Detect it (`ECONNABORTED` / `AbortError`) and show a **Try again** message.
