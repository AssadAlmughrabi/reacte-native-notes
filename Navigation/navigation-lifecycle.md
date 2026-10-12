# Navigation Lifecycle: useFocusEffect, Focus/Blur Events

## The problem

In React Navigation, screens **stay mounted** when you leave them:

- Stack: going from `Home` to `Details` keeps `Home` mounted underneath.
- Tabs: switching tabs keeps the old tab mounted.

So `useEffect(..., [])` runs **only once**, not every time you come back to the screen.

To run code every time a screen **appears** or **disappears**, use **focus** and **blur**.

| Event | When |
| --- | --- |
| **Focus** | The screen becomes visible |
| **Blur** | The user leaves the screen (but it stays mounted) |

---

## 1. useFocusEffect

Runs code when the screen is **focused**. The cleanup runs when it is **blurred**.

```jsx
import { useCallback } from 'react';
import { useFocusEffect } from '@react-navigation/native';

function ProfileScreen() {
  useFocusEffect(
    useCallback(() => {
      console.log('Screen focused');
      loadProfile();               // refresh data every time you come back

      return () => {
        console.log('Screen blurred');   // cleanup
      };
    }, [])
  );

  return <Text>Profile</Text>;
}
```

- **Always wrap the function in `useCallback`**, or it runs on every render.
- Good for: refreshing data, starting/stopping timers, video, camera, listeners.

---

## 2. useIsFocused

Returns `true` or `false`. The component re-renders when it changes.

```jsx
import { useIsFocused } from '@react-navigation/native';

function VideoScreen() {
  const isFocused = useIsFocused();

  return <Video paused={!isFocused} />;
}
```

Good for: changing **what is shown** based on focus.

---

## 3. focus/blur events

Listen with `navigation.addListener`. It returns an unsubscribe function.

```jsx
useEffect(() => {
  const unsubFocus = navigation.addListener('focus', () => {
    console.log('focused');
  });

  const unsubBlur = navigation.addListener('blur', () => {
    console.log('blurred');
  });

  return () => {
    unsubFocus();
    unsubBlur();
  };
}, [navigation]);
```

`useFocusEffect` does the same thing with less code, so prefer it.

---

## Which one to use?

| Need | Use |
| --- | --- |
| Run code on focus, clean up on blur | `useFocusEffect` |
| Show/hide or change UI by focus | `useIsFocused` |
| Only listen to one event | `navigation.addListener('focus' / 'blur')` |
| Run once when the screen first mounts | `useEffect(..., [])` |

---

## Summary

- Screens **stay mounted** when you leave them, so `useEffect` runs only once.
- **Focus** = screen visible, **blur** = screen left.
- `useFocusEffect(useCallback(...))` = run on focus, clean up on blur.
- `useIsFocused()` = `true`/`false` for UI.
- `navigation.addListener('focus' | 'blur')` = the manual way.
