# Pull-to-Refresh

Lets the user **drag a list down to reload** it. A spinner shows at the top while loading.

---

## FlatList

`FlatList` has it built in. Add two props:

```jsx
const [refreshing, setRefreshing] = useState(false);

async function onRefresh() {
  setRefreshing(true);
  try {
    await loadPosts();
  } finally {
    setRefreshing(false);
  }
}

<FlatList
  data={posts}
  keyExtractor={item => String(item.id)}
  renderItem={({ item }) => <Text>{item.title}</Text>}
  refreshing={refreshing}
  onRefresh={onRefresh}
/>
```

- `refreshing`: `true` shows the spinner, `false` hides it.
- `onRefresh`: runs when the user pulls down.

**Remember:** set `refreshing` back to `false` in `finally`, or the spinner never stops.

---

## ScrollView

Use `RefreshControl`:

```jsx
<ScrollView
  refreshControl={
    <RefreshControl refreshing={refreshing} onRefresh={onRefresh} />
  }
>
  {/* content */}
</ScrollView>
```

---

## Summary

- **FlatList:** `refreshing` + `onRefresh`.
- **ScrollView:** `refreshControl={<RefreshControl ... />}`.
- Always set `refreshing` to `false` when done.
