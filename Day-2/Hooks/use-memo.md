# useMemo

`useMemo` **remembers the result of a calculation** between re-renders. It recalculates only when a dependency changes.

```tsx
const value = useMemo(() => {
  return expensiveCalculation(a, b);
}, [a, b]);
```

---

## Use 1: Skip slow calculations

Without `useMemo`, the filter runs on **every** re-render, even when only `isDark` changed.

```tsx
function ProductList({ products }: { products: Product[] }) {
  const [search, setSearch] = useState('');
  const [isDark, setIsDark] = useState(false);

  const filtered = useMemo(() => {
    return products.filter(p => p.name.toLowerCase().includes(search.toLowerCase()));
  }, [products, search]);

  return (
    <FlatList
      data={filtered}
      keyExtractor={item => item.id}
      renderItem={({ item }) => <Text>{item.name}</Text>}
    />
  );
}
```

Now it filters again **only when `products` or `search` changes**.

---

## Use 2: Keep the same object

Objects and arrays created during render are **new every time**. That breaks `React.memo` and triggers effects again.

```tsx
// New object every render, so MemoChild re-renders
<MemoChild style={{ padding: 16 }} />

// Same object, so MemoChild does not re-render
const style = useMemo(() => ({ padding: 16 }), []);
<MemoChild style={style} />
```

Also useful for a Context `value`:

```tsx
const value = useMemo(() => ({ user, login, logout }), [user, login, logout]);
```

---

## When NOT to use it

- **Cheap calculations** like `a + b` or a short string. `useMemo` itself has a cost.
- Values that are **not** passed to memo components or used in effects.

Use it when you **notice** slowness, or for stable objects passed to `memo` / Context.

---

## useMemo vs useCallback

| | useMemo | useCallback |
| --- | --- | --- |
| Remembers | A **value** | A **function** |
| Example | `useMemo(() => items.filter(...), [items])` | `useCallback(() => save(), [])` |

`useCallback(fn, deps)` is the same as `useMemo(() => fn, deps)`.

---

## Summary

- `useMemo` = remember a **calculated value**.
- Recalculates only when **dependencies** change.
- Use for **slow calculations** and **stable objects** (memo props, Context value).
- Skip it for cheap calculations.
