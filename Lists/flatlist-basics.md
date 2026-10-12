# FlatList Basics

`FlatList` shows long lists efficiently. Unlike `ScrollView` + `map()`, it only renders the items **on screen** and recycles the rest.

Use `FlatList` for data arrays. Use `ScrollView` for a small, fixed amount of content.

---

## 1. Basic example

```jsx
import { FlatList, Text, View } from 'react-native';

const users = [
  { id: '1', name: 'Ali' },
  { id: '2', name: 'Sara' },
  { id: '3', name: 'Omar' },
];

function UserList() {
  return (
    <FlatList
      data={users}
      keyExtractor={item => item.id}
      renderItem={({ item }) => <Text>{item.name}</Text>}
    />
  );
}
```

Three props you always need:

| Prop | What it does |
| --- | --- |
| `data` | The array to show |
| `renderItem` | How to draw one item |
| `keyExtractor` | A unique key for each item |

---

## 2. renderItem

A function that receives an object and returns the UI for **one** item.

```jsx
renderItem={({ item, index }) => (
  <View style={{ padding: 16 }}>
    <Text>{index + 1}. {item.name}</Text>
  </View>
)}
```

- `item`: the current item from `data`
- `index`: its position in the array

For cleaner code, move the row into its own component:

```jsx
function UserRow({ user }) {
  return (
    <View style={{ padding: 16 }}>
      <Text>{user.name}</Text>
    </View>
  );
}

<FlatList
  data={users}
  keyExtractor={item => item.id}
  renderItem={({ item }) => <UserRow user={item} />}
/>
```

---

## 3. keyExtractor

Returns a unique **string** key for each item, so React knows which item is which.

```jsx
keyExtractor={item => item.id}
keyExtractor={item => String(item.id)}   // if id is a number
```

- If your items already have a `key` field, `FlatList` uses it automatically.
- Do not use the index if the list can change (add, remove, reorder).

---

## 4. Separators

`ItemSeparatorComponent` draws something **between** items (not before the first or after the last).

```jsx
<FlatList
  data={users}
  keyExtractor={item => item.id}
  renderItem={({ item }) => <UserRow user={item} />}
  ItemSeparatorComponent={() => (
    <View style={{ height: 1, backgroundColor: '#ddd' }} />
  )}
/>
```

---

## 5. Header, footer and empty list

```jsx
<FlatList
  data={users}
  keyExtractor={item => item.id}
  renderItem={({ item }) => <UserRow user={item} />}
  ListHeaderComponent={<Text style={{ fontSize: 20 }}>Users</Text>}
  ListFooterComponent={<Text>End of list</Text>}
  ListEmptyComponent={<Text>No users found</Text>}
/>
```

| Prop | Shows |
| --- | --- |
| `ListHeaderComponent` | At the top, scrolls with the list |
| `ListFooterComponent` | At the bottom (good for a loading spinner) |
| `ListEmptyComponent` | When `data` is empty |

---

## 6. extraData

`FlatList` only re-renders when `data` changes. If a row depends on **other** state (like a selected ID), pass it in `extraData`, or the list will not update.

```jsx
const [selectedId, setSelectedId] = useState(null);

<FlatList
  data={users}
  keyExtractor={item => item.id}
  extraData={selectedId}
  renderItem={({ item }) => (
    <Pressable onPress={() => setSelectedId(item.id)}>
      <Text style={{ color: item.id === selectedId ? 'blue' : 'black' }}>
        {item.name}
      </Text>
    </Pressable>
  )}
/>
```

---

## 7. Other useful props

| Prop | What it does |
| --- | --- |
| `horizontal` | Scroll sideways |
| `numColumns={2}` | Grid layout |
| `onEndReached` | Runs near the end of the list (load more / infinite scroll) |
| `onEndReachedThreshold={0.5}` | How close to the end (0.5 = half a screen) |
| `refreshing` + `onRefresh` | Pull to refresh |
| `contentContainerStyle` | Style the inner content (padding) |

```jsx
<FlatList
  data={users}
  keyExtractor={item => item.id}
  renderItem={({ item }) => <UserRow user={item} />}
  refreshing={loading}
  onRefresh={loadUsers}
  onEndReached={loadMore}
  onEndReachedThreshold={0.5}
/>
```

> Do not put a `FlatList` inside a `ScrollView` going the same direction. It breaks the lazy rendering. Use `ListHeaderComponent` instead.

---

## Summary

- **`data`** = the array, **`renderItem`** = how to draw one item, **`keyExtractor`** = unique key.
- **`ItemSeparatorComponent`** = line between items.
- **Header / footer / empty** components for extra UI.
- **`extraData`** = re-render when other state changes.
- `FlatList` renders lazily, so it is fast for long lists.
