# React.memo

`React.memo` stops a component from **re-rendering** when its **props did not change**.

---

## The problem

When a parent re-renders, **all its children re-render too**, even if their props are the same.

```tsx
function Parent() {
  const [count, setCount] = useState(0);

  return (
    <View>
      <Pressable onPress={() => setCount(count + 1)}>
        <Text>Count: {count}</Text>
      </Pressable>
      <UserCard name="Sara" />   {/* re-renders on every tap, but name never changes */}
    </View>
  );
}
```

---

## The fix

Wrap the child in `React.memo`:

```tsx
import { memo } from 'react';

const UserCard = memo(function UserCard({ name }: { name: string }) {
  console.log('UserCard rendered');
  return <Text>{name}</Text>;
});
```

Now `UserCard` re-renders **only when `name` changes**.

---

## Watch out: functions and objects

`memo` compares props with `===`. A function or object created inside the parent is **new on every render**, so `memo` does not help:

```tsx
<UserCard name="Sara" onPress={() => console.log('hi')} />   // new function each time
```

Fix with `useCallback` (functions) or `useMemo` (objects):

```tsx
const handlePress = useCallback(() => console.log('hi'), []);

<UserCard name="Sara" onPress={handlePress} />   // same function, memo works
```

---

## When to use it

- **List rows** in a `FlatList` (very common in React Native).
- Components that are **slow to render**.
- Components that re-render often with the **same props**.

Do not wrap everything. For small, cheap components, the comparison costs more than it saves.

---

## Summary

- `memo(Component)` = skip re-render if props are the same.
- Compares props with `===`.
- Use `useCallback` / `useMemo` for function and object props.
- Best for list rows and heavy components.
