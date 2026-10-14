# Error Handling

Errors will happen: no internet, server down, bad data, bugs. Good error handling means the app **does not crash** and the user **knows what to do**.

| Error type | Tool |
| --- | --- |
| API / async errors | `try/catch` |
| Errors while rendering UI | **Error Boundary** |
| Crashes in production | Crash reporting (Sentry, Crashlytics) |

---

## 1. API errors with try/catch

```tsx
async function loadProfile() {
  try {
    const res = await api.get('/profile');
    setProfile(res.data);
  } catch (error) {
    setError(getErrorMessage(error));
  } finally {
    setLoading(false);
  }
}
```

- **`try`**: the code that can fail.
- **`catch`**: what to do if it fails.
- **`finally`**: always runs (stop the loading spinner here).

---

## 2. One helper for error messages

Turn any error into a **user-friendly** message, in one place:

```ts
// src/utils/getErrorMessage.ts
import axios from 'axios';

export function getErrorMessage(error: unknown): string {
  if (axios.isAxiosError(error)) {
    if (!error.response) return 'No internet connection. Please try again.';

    const status = error.response.status;
    if (status === 401) return 'Your session expired. Please log in again.';
    if (status === 403) return "You don't have permission to do this.";
    if (status === 404) return 'Not found.';
    if (status >= 500) return 'Server error. Please try again later.';

    return error.response.data?.message ?? 'Something went wrong.';
  }
  return 'Something went wrong.';
}
```

Never show raw errors like `TypeError: undefined is not an object` to the user.

---

## 3. Show the error in the UI

Every screen that loads data has **3 states**: loading, error, success.

```tsx
if (loading) return <ActivityIndicator />;

if (error) {
  return (
    <View style={{ padding: 16, alignItems: 'center' }}>
      <Text>{error}</Text>
      <Pressable onPress={loadProfile}>
        <Text style={{ color: '#2563eb' }}>Try again</Text>
      </Pressable>
    </View>
  );
}

return <ProfileView profile={profile} />;
```

Always give a **"Try again"** button.

---

## 4. Error Boundary (render errors)

`try/catch` does **not** catch errors thrown while rendering components. If a component crashes, the **whole app** shows a red screen (dev) or closes (production).

An **Error Boundary** catches those and shows a fallback screen instead. Easiest with the `react-error-boundary` library:

```bash
npm install react-error-boundary
```

```tsx
import { ErrorBoundary } from 'react-error-boundary';

function ErrorFallback({ resetErrorBoundary }: { resetErrorBoundary: () => void }) {
  return (
    <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
      <Text>Something went wrong.</Text>
      <Pressable onPress={resetErrorBoundary}>
        <Text style={{ color: '#2563eb' }}>Try again</Text>
      </Pressable>
    </View>
  );
}

export default function App() {
  return (
    <ErrorBoundary FallbackComponent={ErrorFallback}>
      <NavigationContainer>
        <RootNavigator />
      </NavigationContainer>
    </ErrorBoundary>
  );
}
```

> Error Boundaries do **not** catch errors in event handlers (`onPress`) or async code. Use `try/catch` there.

---

## 5. Crash reporting (production)

You cannot see users' consoles. Use **Sentry** or **Firebase Crashlytics** to get a report of every crash and error, with the device and the line of code.

---

## Summary

- **`try/catch/finally`** for API and async code.
- One **`getErrorMessage`** helper for friendly messages.
- Every screen: **loading, error (with retry), success**.
- **Error Boundary** catches render crashes and shows a fallback.
- **Sentry / Crashlytics** to see errors in production.
