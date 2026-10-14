# Access and Refresh Tokens

After login, the server does not ask for your password again on every request. Instead, it gives the app **two tokens**.

---

## What they are

| | Access token | Refresh token |
| --- | --- | --- |
| What it is | A "pass" that proves who you are | A "pass" to get a new access token |
| Sent with | **Every** API request | Only the **refresh** request |
| Lifetime | **Short** (e.g. 15 minutes) | **Long** (e.g. 30 days) |
| Example header | `Authorization: Bearer <accessToken>` | Sent in the body to `/auth/refresh` |

Think of a hotel: the **access token** is your room key card (expires soon), the **refresh token** is your ID at the front desk (lets you get a new key card).

---

## Why two tokens?

- If **only a long token** existed and it leaked, an attacker could use it for weeks.
- If **only a short token** existed, the user would have to log in every 15 minutes.

Two tokens give both:

- **Safety:** the token sent everywhere (access) expires fast.
- **Comfort:** the refresh token keeps the user logged in without typing the password.

---

## How it works

```text
1. Login with email + password
      → server returns accessToken + refreshToken

2. Call the API with the accessToken
      → 200 OK

3. 15 minutes later, accessToken expires
      → server returns 401

4. App sends the refreshToken to /auth/refresh
      → server returns a NEW accessToken (often a new refreshToken too)

5. App retries the request with the new accessToken
      → 200 OK

6. Refresh token expired or invalid
      → user must log in again
```

---

## What a token looks like (JWT)

Most access tokens are **JWTs**: three parts separated by dots.

```text
eyJhbGciOiJIUzI1NiJ9.eyJ1c2VySWQiOiIxMjMiLCJleHAiOjE3MDAwMDAwMDB9.abc123signature
      header                        payload                          signature
```

- **Payload** holds data like the user ID and the expiry time (`exp`).
- The payload is only **encoded, not encrypted**: anyone can read it. Never put secrets in it.
- The **signature** lets the server check that nobody changed it.

---

## Summary

- **Access token:** short-lived, sent with every request.
- **Refresh token:** long-lived, used only to get a new access token.
- Two tokens = **safe** (short access) and **comfortable** (no re-login).
- On `401`: refresh and retry. If refresh fails: log in again.
- Where to store them: see `access-and-refresh-tokens.md`.
