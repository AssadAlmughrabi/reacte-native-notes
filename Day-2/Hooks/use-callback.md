# useCallback

`useCallback` keeps the **same function** between re-renders, instead of creating a new one every time.

```tsx
const memoizedFn = useCallback(() => {
  // function body
}, [dependencies]);
```

It returns a new function **only when a dependency changes**.

---

## The problem

Every re-render creates **new functions**. For a child wrapped in `React.memo`, a new function means "props changed", so it re-renders anyway.

```tsx
function Parent() {
  const [count, setCount] = useState(0);

  const handlePress = () => console.log('pressed');   // new function every render

  return (
    <View>
      <Text onPress={() => setCount(count + 1)}>{count}</Text>
      <MyButton onPress={handlePress} />   {/* re-renders every time */}
    </View>
  );
}

const MyButton = memo(function MyButton({ onPress }: { onPress: () => void }) {
  return <Pressable onPress={onPress}><Text>Press</Text></Pressable>;
});
```

---

## The fix

```tsx
const handlePress = useCallback(() => {
  console.log('pressed');
}, []);
```

Now `handlePress` is the **same function** every render, so `MyButton` does not re-render.

---

## Dependencies

If the function uses state or props, put them in the array:

```tsx
const deleteItem = useCallback((id: string) => {
  setItems(prev => prev.filter(item => item.id !== id));
}, []);   // uses the function form, so no deps needed

const saveUser = useCallback(() => {
  api.save(userId);
}, [userId]);   // new function only when userId changes
```

Missing a dependency = the function uses an **old value**.

---

## When to use it

- Passing a function to a child wrapped in **`React.memo`**.
- `renderItem` and handlers in a **`FlatList`**.
- A function used in a **`useEffect`** dependency array.
- Inside **`useFocusEffect`** (required there).

**Not needed** for functions used only in the same component, or passed to normal (non-memo) components.

---

## useCallback vs useMemo

| | useCallback | useMemo |
| --- | --- | --- |
| Keeps the same | **Function** | **Value** (result of a calculation) |
| Example | `useCallback(() => save(), [])` | `useMemo(() => total(items), [items])` |

---

## Summary

- `useCallback` = same function between re-renders.
- Changes only when a **dependency** changes.
- Useful with **`React.memo`**, `FlatList` and effect dependencies.
- Do not use it everywhere, only where it stops real re-renders.
