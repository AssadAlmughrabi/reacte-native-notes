# useRoute

`useRoute` is a hook that gives you the **`route` object** of the current screen in **any** component. You use it mainly to read **params**.

---

## Why you need it

Screens get `route` as a prop automatically. Components **inside** a screen do not. `useRoute` lets them read it without passing it down.

---

## Example

Sending params:

```jsx
navigation.navigate('Details', { id: 42, name: 'Phone' });
```

Reading them in any component of the `Details` screen:

```jsx
import { useRoute } from '@react-navigation/native';
import { Text } from 'react-native';

function DetailsTitle() {
  const route = useRoute();
  const { id, name } = route.params;

  return <Text>{name} (#{id})</Text>;
}
```

---

## What's inside `route`

| Field | Meaning |
| --- | --- |
| `route.params` | Data passed to the screen |
| `route.name` | The screen's name, like `'Details'` |
| `route.key` | A unique ID for this screen instance |

---

## Tips

- If a screen can open **without** params, use a fallback: `route.params?.id`.
- Params should be **simple data** (IDs, strings, numbers). Do not pass functions or big objects. Pass an ID and load the data in the screen.
- Only works inside a component that is **inside `NavigationContainer`**.

---

## useRoute vs useNavigation

| | useRoute | useNavigation |
| --- | --- | --- |
| Gives you | `route` | `navigation` |
| Used to | **Read** params and screen info | **Move** between screens |

---

## Summary

- `useRoute()` = get `route` in any component.
- Read data with `route.params`.
- Use `useNavigation()` to move, `useRoute()` to read.
