# Infinite Scrolling

**Infinite scrolling** loads more items **automatically** when the user reaches the end of the list. There is no "Next page" button. Think Instagram or Twitter feeds.

It uses **pagination** behind the scenes (see `pagination.md`): pagination is **how** data is split into pages, infinite scrolling is **when** the next page loads (on scroll).

---

## The key props

```jsx
<FlatList
  data={items}
  keyExtractor={item => String(item.id)}
  renderItem={({ item }) => <Text>{item.title}</Text>}
  onEndReached={loadMore}
  onEndReachedThreshold={0.5}
  ListFooterComponent={
    loading ? <ActivityIndicator /> :
    !hasMore ? <Text>No more items</Text> : null
  }
/>
```

| Prop | What it does |
| --- | --- |
| `onEndReached` | Runs when the user scrolls near the end |
| `onEndReachedThreshold` | How early to trigger. `0.5` = half a screen before the end |
| `ListFooterComponent` | Spinner while loading, or "No more items" at the end |

---

## The loadMore function

```jsx
async function loadMore() {
  if (loading || !hasMore) return;   // guard
  setLoading(true);

  const data = await fetchItems(page);
  setItems(prev => [...prev, ...data]);
  setHasMore(data.length > 0);
  setPage(prev => prev + 1);

  setLoading(false);
}
```

---

## Watch out for

- **`onEndReached` can fire more than once.** Always check `loading` first, or you load the same page twice.
- **Stop at the end.** Use `hasMore`, or it keeps sending requests forever.
- **Do not put the `FlatList` inside a `ScrollView`.** `onEndReached` fires right away and loads everything.

---

## Summary

- Infinite scroll = load the next page **automatically** on scroll.
- Use `onEndReached` + `onEndReachedThreshold`.
- Guard with `loading` and `hasMore`.
- Show a spinner in `ListFooterComponent`.
