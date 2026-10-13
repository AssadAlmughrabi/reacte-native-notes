# useRef

`useRef` gives you a **box** (`ref.current`) that keeps its value between re-renders. **Changing it does not re-render** the component.

```tsx
const ref = useRef(initialValue);

ref.current;          // read
ref.current = 10;     // write (no re-render)
```

Two main uses:

| Use | Example |
| --- | --- |
| **Access a component** directly | Focus a `TextInput`, scroll a `FlatList` |
| **Store a value** without re-rendering | Timer ID, previous value, "is first render" |

---

## Use 1: Access a component

### Focus a TextInput

```tsx
import { useRef } from 'react';
import { TextInput, Pressable, Text } from 'react-native';

function Form() {
  const passwordRef = useRef<TextInput>(null);

  return (
    <>
      <TextInput
        placeholder="Email"
        returnKeyType="next"
        onSubmitEditing={() => passwordRef.current?.focus()}
      />
      <TextInput ref={passwordRef} placeholder="Password" secureTextEntry />
    </>
  );
}
```

### Scroll a FlatList to the top

```tsx
const listRef = useRef<FlatList>(null);

<FlatList ref={listRef} data={items} renderItem={renderItem} />

<Pressable onPress={() => listRef.current?.scrollToOffset({ offset: 0, animated: true })}>
  <Text>Back to top</Text>
</Pressable>
```

---

## Use 2: Store a value without re-rendering

### Timer ID

```tsx
function Stopwatch() {
  const [seconds, setSeconds] = useState(0);
  const intervalRef = useRef<ReturnType<typeof setInterval> | null>(null);

  const start = () => {
    intervalRef.current = setInterval(() => setSeconds(s => s + 1), 1000);
  };

  const stop = () => {
    if (intervalRef.current) clearInterval(intervalRef.current);
  };

  return (
    <>
      <Text>{seconds}s</Text>
      <Pressable onPress={start}><Text>Start</Text></Pressable>
      <Pressable onPress={stop}><Text>Stop</Text></Pressable>
    </>
  );
}
```

The interval ID is needed later, but showing it on screen is not, so a ref is perfect.

---

## useRef vs useState

| | useRef | useState |
| --- | --- | --- |
| Keeps value between renders | Yes | Yes |
| Changing it re-renders | **No** | **Yes** |
| Update | `ref.current = x` | `setValue(x)` |
| Use for | Values the UI does not show, component access | Values the UI shows |

**Rule:** if the value appears on screen, use **state**. If not, a **ref** is fine.

---

## Summary

- `useRef` = a box (`ref.current`) that survives re-renders.
- Changing it does **not** re-render.
- Use it to **access components** (focus, scroll) and **store values** (timer IDs).
- Do not use it for data shown on screen. Use `useState` for that.
