# Pagination

**Pagination** means loading data in **small pages** (for example 20 items at a time) instead of everything at once. In mobile apps, the next page usually loads when the user scrolls to the end (**infinite scroll**).

---

## Example

```jsx
import { useState, useEffect } from 'react';
import { FlatList, Text, ActivityIndicator } from 'react-native';

function PostList() {
  const [posts, setPosts] = useState([]);
  const [page, setPage] = useState(1);
  const [loading, setLoading] = useState(false);
  const [hasMore, setHasMore] = useState(true);

  async function loadMore() {
    if (loading || !hasMore) return;
    setLoading(true);

    const res = await fetch(`https://api.example.com/posts?page=${page}&limit=20`);
    const data = await res.json();

    setPosts(prev => [...prev, ...data]);   // add the new page
    setHasMore(data.length === 20);         // fewer than 20 = last page
    setPage(prev => prev + 1);
    setLoading(false);
  }

  useEffect(() => {
    loadMore();
  }, []);

  return (
    <FlatList
      data={posts}
      keyExtractor={item => String(item.id)}
      renderItem={({ item }) => <Text>{item.title}</Text>}
      onEndReached={loadMore}
      onEndReachedThreshold={0.5}
      ListFooterComponent={loading ? <ActivityIndicator /> : null}
    />
  );
}
```

---

## How it works

| Part | Job |
| --- | --- |
| `page` | Which page to load next |
| `onEndReached` | Calls `loadMore` when the user is near the end |
| `onEndReachedThreshold={0.5}` | Start loading half a screen before the end |
| `loading` | Stops loading the same page twice |
| `hasMore` | Stops loading after the last page |
| `ListFooterComponent` | Shows a spinner at the bottom while loading |

---

## Summary

- Load data in **pages**, not all at once.
- **Add** each new page to the list: `[...prev, ...data]`.
- Use `onEndReached` to load the next page.
- Check `loading` and `hasMore` to avoid extra requests.
