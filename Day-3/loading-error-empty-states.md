# Loading, Error, and Empty States in the UI

Any screen that loads data can be in **4 states**. Handle **all** of them, not just success.

| State | When | Show |
| --- | --- | --- |
| **Loading** | Waiting for data | Spinner or skeleton |
| **Error** | Request failed | Message + **Try again** button |
| **Empty** | Request worked, but no data | Friendly message + next action |
| **Success** | Data arrived | The real content |

---

## The pattern

Check the states **in order**, and return early:

```tsx
function OrdersScreen() {
  const [orders, setOrders] = useState<Order[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  async function loadOrders() {
    setLoading(true);
    setError(null);
    try {
      const res = await api.get('/orders');
      setOrders(res.data);
    } catch (e) {
      setError(getErrorMessage(e));
    } finally {
      setLoading(false);
    }
  }

  useEffect(() => {
    loadOrders();
  }, []);

  if (loading) return <LoadingView />;
  if (error) return <ErrorView message={error} onRetry={loadOrders} />;
  if (orders.length === 0) return <EmptyView message="No orders yet" />;

  return (
    <FlatList
      data={orders}
      keyExtractor={item => item.id}
      renderItem={({ item }) => <OrderRow order={item} />}
    />
  );
}
```

---

## Reusable state components

Make them once, use them on every screen.

```tsx
// src/components/StateViews.tsx
import { View, Text, ActivityIndicator, Pressable, StyleSheet } from 'react-native';

export function LoadingView() {
  return (
    <View style={styles.center}>
      <ActivityIndicator size="large" />
    </View>
  );
}

export function ErrorView({ message, onRetry }: { message: string; onRetry: () => void }) {
  return (
    <View style={styles.center}>
      <Text style={styles.text}>{message}</Text>
      <Pressable style={styles.button} onPress={onRetry}>
        <Text style={styles.buttonText}>Try again</Text>
      </Pressable>
    </View>
  );
}

export function EmptyView({ message }: { message: string }) {
  return (
    <View style={styles.center}>
      <Text style={styles.text}>{message}</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  center: { flex: 1, justifyContent: 'center', alignItems: 'center', padding: 24, gap: 12 },
  text: { fontSize: 16, color: '#555', textAlign: 'center' },
  button: { backgroundColor: '#2563eb', paddingHorizontal: 20, paddingVertical: 10, borderRadius: 8 },
  buttonText: { color: 'white', fontWeight: 'bold' },
});
```

---

## Empty state inside a FlatList

`FlatList` has a built-in prop for it:

```tsx
<FlatList
  data={orders}
  renderItem={renderItem}
  ListEmptyComponent={<EmptyView message="No orders yet" />}
  contentContainerStyle={{ flexGrow: 1 }}   // lets the empty view center
/>
```

---

## Tips

- **Loading:** for lists, a **skeleton** (gray placeholder rows) feels faster than a spinner.
- **Error:** use a **friendly** message, never a raw error. Always add **Try again**.
- **Empty:** say **why** it's empty and what to do next ("No orders yet. Start shopping").
- **Refreshing:** when data is already shown, do not replace it with a full spinner. Use pull-to-refresh.

---

## Summary

- 4 states: **loading, error, empty, success**.
- Check them in order with **early returns**.
- Build **reusable** `LoadingView`, `ErrorView`, `EmptyView`.
- Error = message + **retry**. Empty = message + **next action**.
- Use `ListEmptyComponent` for empty lists.
