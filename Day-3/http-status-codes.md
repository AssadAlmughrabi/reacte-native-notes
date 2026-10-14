# HTTP Status Codes

Every API response has a **status code**: a 3-digit number that says **what happened**.

| Range | Meaning |
| --- | --- |
| **2xx** | Success |
| **3xx** | Redirect |
| **4xx** | **Client** error (the app sent something wrong) |
| **5xx** | **Server** error (the backend failed) |

---

## The common ones

### 2xx: Success

| Code | Name | Meaning |
| --- | --- | --- |
| **200** | OK | Request worked, data returned |
| **201** | Created | New item created (after `POST`) |
| **204** | No Content | Worked, but nothing to return (often after `DELETE`) |

### 3xx: Redirect

| Code | Name | Meaning |
| --- | --- | --- |
| **301** | Moved Permanently | The URL changed for good |
| **304** | Not Modified | Use your cached copy |

### 4xx: Client errors

| Code | Name | Meaning | What the app should do |
| --- | --- | --- | --- |
| **400** | Bad Request | Invalid data sent | Show a validation message |
| **401** | Unauthorized | Not logged in or **token expired** | Refresh the token, or log out |
| **403** | Forbidden | Logged in, but **not allowed** | Show "no permission" |
| **404** | Not Found | Item or URL does not exist | Show "not found" |
| **409** | Conflict | Already exists (e.g. email taken) | Show the conflict message |
| **422** | Unprocessable Entity | Data format OK, but fails validation | Show field errors |
| **429** | Too Many Requests | Rate limited | Wait and try again later |

### 5xx: Server errors

| Code | Name | Meaning | What the app should do |
| --- | --- | --- | --- |
| **500** | Internal Server Error | Backend crashed | Show "something went wrong", allow retry |
| **502** | Bad Gateway | Server behind a proxy failed | Retry later |
| **503** | Service Unavailable | Server down or overloaded | Retry later |
| **504** | Gateway Timeout | Server took too long | Retry later |

---

## 401 vs 403

- **401** = "Who are you?" You are **not logged in** (or the token expired).
- **403** = "I know who you are, but **you can't do this**."

---

## Handling them with Axios

Axios **throws** for any status outside **2xx**.

```ts
try {
  const res = await api.get('/profile');
  console.log(res.status, res.data);   // 200
} catch (error) {
  if (axios.isAxiosError(error)) {
    const status = error.response?.status;

    if (!error.response) {
      showMessage('No internet connection');      // network error, no status
    } else if (status === 404) {
      showMessage('Not found');
    } else if (status && status >= 500) {
      showMessage('Server error, try again later');
    }
  }
}
```

> **No response at all** (no status) = network problem: offline, timeout, or server unreachable.

---

## Summary

- **2xx** success, **4xx** app's fault, **5xx** server's fault.
- **200** OK, **201** created, **204** no content.
- **401** not logged in (refresh token), **403** not allowed, **404** not found.
- **500+** server problem: show an error and allow retry.
- Axios throws on non-2xx; no `error.response` = network error.
