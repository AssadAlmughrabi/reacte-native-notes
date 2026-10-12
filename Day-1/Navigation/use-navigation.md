# useNavigation

`useNavigation` is a hook that gives you the **`navigation` object** in **any** component, not only in screens.

---

## Why you need it

Screens get `navigation` as a prop automatically. But components **inside** a screen (a button, a card) do not.

Without the hook, you would pass `navigation` down through every component. With the hook, any component can get it directly.

---

## Example

```jsx
import { useNavigation } from '@react-navigation/native';
import { Pressable, Text } from 'react-native';

function ProductCard({ product }) {
  const navigation = useNavigation();

  return (
    <Pressable onPress={() => navigation.navigate('Details', { id: product.id })}>
      <Text>{product.name}</Text>
    </Pressable>
  );
}
```

`ProductCard` is not a screen, but it can still navigate.

---

## What you can do with it

| Method | What it does |
| --- | --- |
| `navigate('Name', params)` | Go to a screen |
| `goBack()` | Go back one screen |
| `push('Name', params)` | Add a new copy of a screen (stack only) |
| `replace('Name')` | Replace the current screen |
| `setOptions({ title })` | Change the header |

---

## useRoute (its partner)

To read the current screen's **params** from any component, use `useRoute`:

```jsx
import { useRoute } from '@react-navigation/native';

function DetailsHeader() {
  const route = useRoute();
  return <Text>Item {route.params.id}</Text>;
}
```

---

## Rules

- Only works inside a component that is **inside `NavigationContainer`**.
- It is a hook, so call it at the **top level** of the component.

---

## Summary

- `useNavigation()` = get `navigation` in any component.
- No need to pass `navigation` as a prop.
- Use `useRoute()` to read params.
