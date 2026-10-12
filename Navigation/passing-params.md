# Passing Params

**Params** are data you send to a screen when you navigate to it, like function arguments for screens.

---

## 1. Send params

Pass an object as the second argument of `navigate`:

```jsx
navigation.navigate('Details', { id: 42, title: 'Phone' });
```

---

## 2. Read params

In the screen, use `route.params`:

```jsx
function DetailsScreen({ route }) {
  const { id, title } = route.params;

  return <Text>{title} (#{id})</Text>;
}
```

In a nested component, use `useRoute()` (see `use-route.md`).

---

## 3. Default params

Give a screen starting params with `initialParams`:

```jsx
<Stack.Screen
  name="Details"
  component={DetailsScreen}
  initialParams={{ id: 0 }}
/>
```

Or use a fallback when reading: `route.params?.id ?? 0`.

---

## 4. Update params

A screen can change its own params:

```jsx
navigation.setParams({ title: 'New title' });
```

---

## 5. Send data back to the previous screen

Go back to the screen **by name** with new params. `merge: true` keeps its old params and adds the new ones.

```jsx
// In EditScreen
navigation.popTo('Profile', { updatedName: 'Sara' }, { merge: true });
```

```jsx
// In ProfileScreen
useEffect(() => {
  if (route.params?.updatedName) {
    // use the new value
  }
}, [route.params?.updatedName]);
```

---

## Tips

- Pass **simple data**: IDs, strings, numbers.
- Do **not** pass functions or large objects. Pass an ID and load the data in the screen.
- For data many screens need (logged-in user, theme), use global state (Context, Redux, Zustand) instead of params.

---

## Summary

- **Send:** `navigation.navigate('Screen', { key: value })`.
- **Read:** `route.params`.
- **Default:** `initialParams`. **Update:** `setParams`.
- **Send back:** `popTo('PreviousScreen', params)`.
- Keep params small: pass IDs, not whole objects.
