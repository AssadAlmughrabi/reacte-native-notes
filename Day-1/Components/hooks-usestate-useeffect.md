# useState and useEffect

## useState

Lets a component **remember a value**. When the value changes, the component re-renders.

```jsx
import { useState } from 'react';
import { View, Text, Pressable } from 'react-native';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <View>
      <Text>Count: {count}</Text>
      <Pressable onPress={() => setCount(count + 1)}>
        <Text>Add</Text>
      </Pressable>
    </View>
  );
}
```

- `count`: the current value
- `setCount`: the function that changes it
- `0`: the starting value

**Remember:** never change state directly (`count = 5`). Always use the setter (`setCount(5)`).

---

## useEffect

Runs code **after** the component renders. Used for things like fetching data, timers and listeners.

```jsx
useEffect(() => {
  // code to run
}, [dependencies]);
```

The array at the end decides **when** it runs:

| Code | Runs |
| --- | --- |
| `useEffect(fn, [])` | Once, when the component first appears |
| `useEffect(fn, [value])` | First time, and again whenever `value` changes |
| `useEffect(fn)` | After every render |

### Example

```jsx
import { useState, useEffect } from 'react';
import { Text } from 'react-native';

function Timer() {
  const [seconds, setSeconds] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setSeconds(prev => prev + 1);
    }, 1000);

    return () => clearInterval(id); // cleanup
  }, []);

  return <Text>{seconds}s</Text>;
}
```

- `[]` means the timer starts once.
- The `return` function is the **cleanup**. It runs when the component is removed, so the timer stops.

### Cleanup

The function you **return** from `useEffect` is the cleanup. Use it to stop whatever the effect started.

```jsx
useEffect(() => {
  // start something

  return () => {
    // stop it (cleanup)
  };
}, []);
```

**When it runs:**

- When the component is removed from the screen.
- Before the effect runs again (when a dependency changes).

**Why it matters:** without cleanup, timers and listeners keep running after the screen closes. This wastes memory and can cause bugs.

| Effect starts | Cleanup stops |
| --- | --- |
| `setInterval(...)` | `clearInterval(id)` |
| `setTimeout(...)` | `clearTimeout(id)` |
| `Keyboard.addListener(...)` | `subscription.remove()` |

---

## Summary

- **useState** = remember a value.
- **useEffect** = do something after render.
- **Cleanup** = stop what the effect started.
