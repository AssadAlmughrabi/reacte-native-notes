# Interceptors

An **interceptor** is a function that sits **between your app and the server**. It runs automatically on **every** request or response, so you write shared logic **once**.

```text
App  →  [request interceptor]  →  Server
App  ←  [response interceptor] ←  Server
```

Without interceptors, you repeat the same code (add token, handle errors) in every API call.

---

## Request interceptors

Run **before** the request is sent. They can change the request.

| Use | Example |
| --- | --- |
| Add auth token | `Authorization: Bearer <token>` |
| Add language | `Accept-Language: ar` |
| Add app info | App version, platform |
| Logging | Print every request in development |

```ts
api.interceptors.request.use(config => {
  config.headers['Accept-Language'] = i18n.language;
  config.headers['X-Platform'] = Platform.OS;

  if (__DEV__) console.log('→', config.method?.toUpperCase(), config.url);

  return config;   // always return config
});
```

---

## Response interceptors

Run **after** the response comes back. They can change the data or handle errors.

| Use | Example |
| --- | --- |
| Unwrap data | Return `response.data` directly |
| Handle 401 | Refresh the token and retry (see `axios-interceptors.md`) |
| Global errors | Show a toast for server errors |
| Logging | Print every response in development |

```ts
api.interceptors.response.use(
  response => {
    if (__DEV__) console.log('←', response.status, response.config.url);
    return response;
  },
  error => {
    if (!error.response) {
      showToast('No internet connection');
    } else if (error.response.status >= 500) {
      showToast('Server error, try again later');
    }
    return Promise.reject(error);   // still let the caller handle it
  }
);
```

---

## Rules

- A request interceptor must **return `config`**.
- A response interceptor must **return the response**, or **`Promise.reject(error)`** for errors.
- Interceptors run **in order**: request ones before sending, response ones after receiving.
- Remove one with `api.interceptors.request.eject(id)` (the `use` call returns the `id`).

---

## Summary

- Interceptor = code that runs on **every** request or response.
- **Request:** add token, language, headers, logging.
- **Response:** handle errors globally, refresh tokens, logging.
- Write it once, every API call uses it.
