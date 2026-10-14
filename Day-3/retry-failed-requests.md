# Retry Failed Requests

Mobile networks drop often. Many failures are **temporary**, so trying again a moment later often works.

---

## When to retry

| Retry | Do NOT retry |
| --- | --- |
| No internet / network error | **400** bad data (it will fail again) |
| Timeout | **401 / 403** (auth problem, not temporary) |
| **5xx** server errors | **404** not found |
| **429** too many requests (wait longer) | **422** validation errors |

**Rule:** retry only when the problem might fix itself.

---

## Be careful with POST

Retrying a `POST` can **create things twice** (two orders, two payments).

- Safe to retry: `GET`, `PUT`, `DELETE`
- `POST`: only if the backend supports it (an **idempotency key**)

---

## Exponential backoff

Wait **longer** after each failure, so you do not flood a struggling server:

```text
Try 1 fails → wait 1s
Try 2 fails → wait 2s
Try 3 fails → wait 4s
Give up → show error
```

---

## Simple retry helper

```ts
// src/utils/withRetry.ts
import axios from 'axios';

const sleep = (ms: number) => new Promise(resolve => setTimeout(resolve, ms));

function shouldRetry(error: unknown) {
  if (!axios.isAxiosError(error)) return false;
  if (!error.response) return true;                 // network error / timeout
  const status = error.response.status;
  return status >= 500 || status === 429;
}

export async function withRetry<T>(request: () => Promise<T>, retries = 3): Promise<T> {
  for (let attempt = 0; ; attempt++) {
    try {
      return await request();
    } catch (error) {
      if (attempt >= retries || !shouldRetry(error)) throw error;
      await sleep(1000 * 2 ** attempt);             // 1s, 2s, 4s
    }
  }
}
```

```ts
const res = await withRetry(() => api.get('/orders'));
```

---

## Using a library

**`axios-retry`** adds retries to every request:

```bash
npm install axios-retry
```

```ts
import axiosRetry from 'axios-retry';

axiosRetry(api, {
  retries: 3,
  retryDelay: axiosRetry.exponentialDelay,
});
```

By default it retries only **network errors** and **5xx** on safe methods (`GET`, `PUT`, `DELETE`...).

> **React Query** also retries failed queries automatically (3 times by default).

---

## Manual retry

After all automatic retries fail, give the user a **Try again** button (see `loading-error-empty-states.md`).

---

## Summary

- Retry **temporary** errors: network, timeout, 5xx, 429.
- Do **not** retry 4xx errors like 400, 401, 404.
- Be careful retrying **POST** (duplicates).
- Use **exponential backoff**: 1s, 2s, 4s, then give up.
- Use a helper, `axios-retry`, or React Query. End with a **Try again** button.
