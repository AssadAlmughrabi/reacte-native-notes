# Custom Hooks

A **custom hook** is your own function that uses other hooks (`useState`, `useEffect`...) so you can **reuse logic** in many components.

---

## Rules

- The name **must start with `use`** (`useToggle`, `useKeyboard`).
- It can call other hooks.
- Call it at the **top level** of a component, like any hook.
- It shares **logic**, not state: each component that calls it gets **its own** state.

---

## Example 1: useToggle

```tsx
// src/hooks/useToggle.ts
import { useState } from 'react';

export function useToggle(initial = false) {
  const [value, setValue] = useState(initial);
  const toggle = () => setValue(prev => !prev);

  return [value, toggle] as const;
}
```

```tsx
function SettingsScreen() {
  const [isDark, toggleDark] = useToggle();

  return (
    <Pressable onPress={toggleDark}>
      <Text>Dark mode: {isDark ? 'On' : 'Off'}</Text>
    </Pressable>
  );
}
```

---

## Example 2: useKeyboardVisible(React Native)

```tsx
// src/hooks/useKeyboardVisible.ts
import { useEffect, useState } from 'react';
import { Keyboard } from 'react-native';

export function useKeyboardVisible() {
  const [visible, setVisible] = useState(false);

  useEffect(() => {
    const show = Keyboard.addListener('keyboardDidShow', () => setVisible(true));
    const hide = Keyboard.addListener('keyboardDidHide', () => setVisible(false));

    return () => {
      show.remove();
      hide.remove();
    };
  }, []);

  return visible;
}
```

```tsx
function LoginScreen() {
  const keyboardVisible = useKeyboardVisible();

  return (
    <View>
      {!keyboardVisible && <Image source={require('./logo.png')} />}
      <TextInput placeholder="Email" />
    </View>
  );
}
```

The logo hides while the keyboard is open. Any screen can reuse this hook.

---

## When to make a custom hook

- The same `useState` + `useEffect` code appears in **two or more** components.
- A component has a lot of logic, and you want the JSX to stay clean.

Put them in a `src/hooks/` folder.

---

## Summary

- Custom hook = a function starting with **`use`** that uses other hooks.
- Use it to **reuse logic** and keep components clean.
- Each component gets **its own state** from the hook.
