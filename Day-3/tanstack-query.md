# TanStack Query (React Query)

**TanStack Query** manages **server state**: fetching, caching, loading/error states, refetching and pagination. It replaces most `useState` + `useEffect` fetch code.

| Part | Job |
| --- | --- |
| **Queries** (`useQuery`) | **Read** data (GET) |
| **Mutations** (`useMutation`) | **Change** data (POST, PUT, DELETE) |
| **Caching** | Keep data, avoid fetching it again |
| **`useInfiniteQuery`** | Pagination / infinite scroll |

---

## Install and setup

```bash
npm install @tanstack/react-query
```

```tsx
// App.tsx
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';

const queryClient = new QueryClient();

export default function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <NavigationContainer>
        <RootNavigator />
      </NavigationContainer>
    </QueryClientProvider>
  );
}
```

---

## 1. Queries (read data)

```tsx
import { useQuery } from '@tanstack/react-query';

function OrdersScreen() {
  const { data, isPending, error, refetch, isRefetching } = useQuery({
    queryKey: ['orders'],
    queryFn: () => api.get('/orders').then(res => res.data),
  });

  if (isPending) return <LoadingView />;
  if (error) return <ErrorView message={getErrorMessage(error)} onRetry={refetch} />;

  return (
    <FlatList
      data={data}
      keyExtractor={item => item.id}
      renderItem={({ item }) => <OrderRow order={item} />}
      refreshing={isRefetching}
      onRefresh={refetch}
    />
  );
}
```

| Option / value | Meaning |
| --- | --- |
| `queryKey` | Unique name for this data (used for the cache) |
| `queryFn` | Function that fetches the data |
| `data` | The result |
| `isPending` | First load, no data yet |
| `error` | The error, if it failed |
| `refetch` | Fetch again (pull-to-refresh, retry button) |

### Query keys with params

```tsx
useQuery({
  queryKey: ['order', orderId],              // different id = different cache entry
  queryFn: () => api.get(`/orders/${orderId}`).then(res => res.data),
  enabled: !!orderId,                        // wait until orderId exists
});
```

---

## 2. Mutations (change data)

```tsx
import { useMutation, useQueryClient } from '@tanstack/react-query';

function AddOrderButton() {
  const queryClient = useQueryClient();

  const { mutate, isPending } = useMutation({
    mutationFn: (newOrder: NewOrder) => api.post('/orders', newOrder),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['orders'] });   // refetch the list
    },
    onError: error => {
      showMessage(getErrorMessage(error));
    },
  });

  return (
    <Pressable disabled={isPending} onPress={() => mutate({ productId: '1', qty: 1 })}>
      <Text>{isPending ? 'Saving...' : 'Add order'}</Text>
    </Pressable>
  );
}
```

- `mutate(data)` runs the request.
- `invalidateQueries` marks the cached list as old, so it **refetches** with the new item.

---

## 3. Caching

Data is saved by its `queryKey`. Open the same screen again and it shows **instantly** from the cache, then refreshes in the background.

| Setting | Meaning | Default |
| --- | --- | --- |
| `staleTime` | How long data counts as **fresh** (no refetch) | `0` (stale right away) |
| `gcTime` | How long **unused** data stays in memory | 5 minutes |

```tsx
useQuery({
  queryKey: ['categories'],
  queryFn: fetchCategories,
  staleTime: 5 * 60 * 1000,   // fresh for 5 minutes: no refetch on every screen visit
});
```

**Fresh** = use the cache, no request. **Stale** = show the cache, but refetch in the background.

> Also built in: **3 retries** on failure, and two screens with the same `queryKey` share **one** request.

---

## 4. useInfiniteQuery (infinite scroll)

```tsx
import { useInfiniteQuery } from '@tanstack/react-query';

function PostList() {
  const { data, fetchNextPage, hasNextPage, isFetchingNextPage, isPending } = useInfiniteQuery({
    queryKey: ['posts'],
    queryFn: ({ pageParam }) =>
      api.get('/posts', { params: { page: pageParam, limit: 20 } }).then(res => res.data),
    initialPageParam: 1,
    getNextPageParam: (lastPage, allPages) =>
      lastPage.length === 20 ? allPages.length + 1 : undefined,   // undefined = no more pages
  });

  const posts = data?.pages.flat() ?? [];

  if (isPending) return <LoadingView />;

  return (
    <FlatList
      data={posts}
      keyExtractor={item => String(item.id)}
      renderItem={({ item }) => <Text>{item.title}</Text>}
      onEndReached={() => hasNextPage && !isFetchingNextPage && fetchNextPage()}
      onEndReachedThreshold={0.5}
      ListFooterComponent={isFetchingNextPage ? <ActivityIndicator /> : null}
    />
  );
}
```

| Part | Meaning |
| --- | --- |
| `initialPageParam` | First page number |
| `getNextPageParam` | Returns the next page number, or `undefined` when done |
| `data.pages` | Array of pages, `.flat()` to get one list |
| `fetchNextPage` | Load the next page |
| `hasNextPage` | `false` after the last page |

---

## Summary

- Wrap the app in **`QueryClientProvider`**.
- **`useQuery`** = read data, with `queryKey` + `queryFn`. Gives `data`, `isPending`, `error`, `refetch`.
- **`useMutation`** = change data. Use `invalidateQueries` in `onSuccess` to refresh lists.
- **Caching** by `queryKey`. `staleTime` = how long data stays fresh.
- **`useInfiniteQuery`** = pages with `getNextPageParam` + `fetchNextPage`.
