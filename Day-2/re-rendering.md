# Re-rendering: When and Why It Happens

A **re-render** means React **runs your component function again** to see if the UI should change. If nothing changed, the screen is not updated, but the function still ran.

---

## The 4 reasons a component re-renders

| Reason | Example |
| --- | --- |
| 1. **Its state changes** | `setCount(count + 1)` |
| 2. **Its parent re-renders** | Parent's state changes, so all children re-render |
| 3. **A context it uses changes** | `useTheme()` and the theme changes |
| 4. **A hook it uses changes** | A custom hook's internal state changes |

**Props changing is not a separate reason.** Props only change because the parent re-rendered (reason 2).

---

## 1. State changes

```tsx
function Counter() {
  const [count, setCount] = useState(0);
  console.log('Counter rendered');

  return (
    <Pressable onPress={() => setCount(count + 1)}>
      <Text>{count}</Text>
    </Pressable>
  );
}
```

Every tap logs `Counter rendered`.

> Setting state to the **same value** (`setCount(count)`) skips the re-render.

---

## 2. Parent re-renders

```tsx
function Parent() {
  const [count, setCount] = useState(0);

  return (
    <View>
      <Text onPress={() => setCount(count + 1)}>{count}</Text>
      <Child />    {/* re-renders too, even with no props */}
    </View>
  );
}
```

When `Parent` re-renders, **every child inside it re-renders**, even if nothing it uses changed. Fix: `React.memo` (see `react-memo.md`).

---

## 3. Context changes

Every component that calls `useContext(SomeContext)` re-renders when that context's `value` changes, even if it uses only a small part of it.

---

## What does NOT cause a re-render

- Changing a normal variable: `let x = 5; x = 6;`
- Changing a `useRef` value: `ref.current = 10`
- Mutating state directly: `items.push(newItem)` (React does not notice, so use `setItems([...items, newItem])`)

---

## Is re-rendering bad?

**Usually no.** Re-renders are fast and normal. Only fix them when you see a real problem: laggy lists, slow screens, animations stuttering.

Tools to reduce re-renders:

| Tool | Use for |
| --- | --- |
| `React.memo` | Skip re-render when props are the same |
| `useCallback` | Keep the same function between renders |
| `useMemo` | Keep the same object/value between renders |
| Split state / context | Smaller pieces change less often |
| Zustand selectors | Re-render only when the used part changes |

---

## Summary

- Re-render = React runs the component function again.
- Happens when: **state** changes, **parent** re-renders, **context** changes.
- Changing variables, refs, or mutating state does **not** re-render.
- Re-renders are normal. Optimize with `memo`, `useCallback`, `useMemo` only when needed.
