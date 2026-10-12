# Keys

A **key** is a unique ID you give each item in a list. React uses it to know **which item is which** when the list changes (add, remove, reorder).

---

## 1. Keys with map()

```jsx
const users = [
  { id: '1', name: 'Ali' },
  { id: '2', name: 'Sara' },
];

<View>
  {users.map(user => (
    <Text key={user.id}>{user.name}</Text>
  ))}
</View>
```

- Put `key` on the **outermost** element returned from `map`.
- Without it, you get the warning: `Each child in a list should have a unique "key" prop.`

---

## 2. Keys with FlatList

`FlatList` uses `keyExtractor` instead of a `key` prop.

```jsx
<FlatList
  data={users}
  keyExtractor={item => item.id}
  renderItem={({ item }) => <Text>{item.name}</Text>}
/>
```

- `keyExtractor` must return a **string**. Use `String(item.id)` if the ID is a number.
- If your items already have a `key` field, `FlatList` uses it automatically.

---

## 3. Why keys matter

When the list changes, React compares old and new items by key:

- **Same key:** keep the item, just update it.
- **New key:** create a new item.
- **Missing key:** remove that item.

With good keys, React updates only what changed. With bad keys, it can mix up items, for example showing text typed in one row's `TextInput` in another row.

---

## 4. Good and bad keys

| Key | Good? | Why |
| --- | --- | --- |
| `item.id` from your data | Yes | Unique and never changes |
| Array index (`index`) | Only for static lists | Breaks when items are added, removed or reordered |
| `Math.random()` | No | New key every render, so React rebuilds every item |

```jsx
// Bad (if the list can change)
{users.map((user, index) => <Row key={index} user={user} />)}

// Good
{users.map(user => <Row key={user.id} user={user} />)}
```

**Rules:**

- Keys must be **unique among siblings** (not across the whole app).
- Keys must be **stable**: the same item always gets the same key.

---

## 5. Bonus: use a key to reset a component

Changing a component's `key` makes React throw it away and create a fresh one, with fresh state.

```jsx
<ProfileForm key={userId} userId={userId} />
```

When `userId` changes, the form resets instead of keeping the old user's input.

---

## Summary

- **Key** = unique, stable ID for each list item.
- `map()`: put `key={item.id}` on the outer element.
- `FlatList`: use `keyExtractor={item => item.id}`.
- Avoid index (for changing lists) and `Math.random()`.
