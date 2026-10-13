# Server State vs UI State

App data comes in two very different kinds. Treating them the same is a common beginner mistake.

| | UI state | Server state |
| --- | --- | --- |
| Where it lives | **Only in the app** | **On the server** (the app has a copy) |
| Who owns it | Your app | The backend |
| Examples | Is modal open, selected tab, input text, theme | Users, posts, products, orders |
| Can it be out of date? | No | **Yes**, someone else can change it on the server |
| Tool | `useState`, Context, Zustand, Redux | **React Query (TanStack Query)** |

---

## UI state

Data your app **creates and controls**. Nobody else can change it.

```tsx
const [isModalOpen, setIsModalOpen] = useState(false);
const [selectedTab, setSelectedTab] = useState('home');
```

Simple: set it, read it, done.

---

## Server state

Data that **lives on a server**. Your app only shows a **copy**, which can become **stale** (out of date).

Handling it yourself needs a lot of code:

```tsx
const [posts, setPosts] = useState([]);
const [loading, setLoading] = useState(true);
const [error, setError] = useState(null);

useEffect(() => {
  fetch('https://api.example.com/posts')
    .then(res => res.json())
    .then(setPosts)
    .catch(setError)
    .finally(() => setLoading(false));
}, []);
```

And this still does **not** handle:

- **Caching:** going back to the screen fetches again
- **Refetching:** data gets old while the user looks at it
- **Sharing:** two screens fetch the same data twice
- **Retries** when the network fails
- **Pagination**, pull-to-refresh, updating after a change

---

## The fix

Use **React Query** for server state. It handles loading, error, caching and refetching for you:

```tsx
const { data, isLoading, error } = useQuery({
  queryKey: ['posts'],
  queryFn: () => fetch('https://api.example.com/posts').then(res => res.json()),
});
```

---

## The rule

- **UI state** → `useState` (local) or Zustand / Context (global).
- **Server state** → React Query.
- **Do not** put server data in Zustand or Redux "by hand". Let React Query manage it.

---

## Summary

- **UI state** = owned by the app (modal open, theme, input).
- **Server state** = owned by the server, the app has a copy that can go stale.
- Server state needs caching, refetching and loading/error handling.
- Use **React Query** for server state (Day 3), and `useState` / Zustand for UI state.
