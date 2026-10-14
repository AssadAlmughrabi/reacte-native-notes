# Request Cancellation (AbortController)

**Cancelling a request** means stopping it before the response arrives, because you **no longer need it**.

When to cancel:

- The user **leaves the screen** before the data loads.
- The user **types in a search box**: cancel the old search when a new one starts.
- A **timeout** is reached.

Without cancelling, an old response can arrive late and **overwrite newer data**, or try to update a screen that is gone.

---

## AbortController basics

```ts
const controller = new AbortController();

fetch(url, { signal: controller.signal });   // connect the request

controller.abort();                          // cancel it
```

- `controller.signal`: pass it to the request.
- `controller.abort()`: cancels every request using that signal.
- One controller = **one use**. Create a new one for each request.

---

## Cancel when leaving the screen

Abort in the `useEffect` **cleanup**:

```tsx
useEffect(() => {
  const controller = new AbortController();

  api.get('/orders', { signal: controller.signal })
    .then(res => setOrders(res.data))
    .catch(error => {
      if (axios.isCancel(error)) return;   // cancelled on purpose, ignore
      setError(getErrorMessage(error));
    });

  return () => controller.abort();         // screen closed: cancel
}, []);
```

Works the same with Axios (`signal` option) and `fetch`.

---

## Cancel old searches

```tsx
function SearchScreen() {
  const [query, setQuery] = useState('');
  const [results, setResults] = useState([]);

  useEffect(() => {
    if (!query) return;
    const controller = new AbortController();

    api.get('/search', { params: { q: query }, signal: controller.signal })
      .then(res => setResults(res.data))
      .catch(error => {
        if (!axios.isCancel(error)) console.log(error);
      });

    return () => controller.abort();   // new letter typed: cancel old search
  }, [query]);

  return <TextInput value={query} onChangeText={setQuery} />;
}
```

Each new `query` runs the cleanup first, which cancels the previous request. Only the **latest** search can update the results.

---

## Detecting a cancel

| Library | Check |
| --- | --- |
| Axios | `axios.isCancel(error)` |
| fetch | `error.name === 'AbortError'` |

Do **not** show an error message for a cancel. It was on purpose.

---

## Summary

- `AbortController` cancels requests you no longer need.
- Pass `controller.signal` to the request, call `controller.abort()` to cancel.
- Abort in the **`useEffect` cleanup**: on leaving the screen and before each new search.
- Ignore cancel errors (`axios.isCancel` / `AbortError`).
