# SectionList

`SectionList` is a `FlatList` with **groups**. Each group has a **header** and its own items. Think of a contacts list grouped by letter, or a menu grouped by category.

---

## Example

```jsx
import { SectionList, Text, View } from 'react-native';

const menu = [
  { title: 'Drinks', data: ['Coffee', 'Tea', 'Juice'] },
  { title: 'Food', data: ['Burger', 'Pizza'] },
  { title: 'Dessert', data: ['Cake', 'Ice cream'] },
];

function Menu() {
  return (
    <SectionList
      sections={menu}
      keyExtractor={(item, index) => item + index}
      renderItem={({ item }) => (
        <Text style={{ padding: 12 }}>{item}</Text>
      )}
      renderSectionHeader={({ section }) => (
        <Text style={{ padding: 12, fontWeight: 'bold', backgroundColor: '#eee' }}>
          {section.title}
        </Text>
      )}
    />
  );
}
```

---

## Data shape

`sections` is an array. Each section **must** have a `data` array. Other fields (like `title`) are up to you.

```js
[
  { title: 'A', data: [...] },
  { title: 'B', data: [...] },
]
```

---

## Main props

| Prop | What it does |
| --- | --- |
| `sections` | The grouped data |
| `renderItem` | How to draw one item |
| `renderSectionHeader` | How to draw a section header |
| `keyExtractor` | A unique key for each item |
| `stickySectionHeadersEnabled` | Headers stick to the top while scrolling (default on iOS) |

It also supports the same extras as `FlatList`: `ItemSeparatorComponent`, `ListEmptyComponent`, `onEndReached`, `refreshing` + `onRefresh`.

---

## FlatList vs SectionList

| | FlatList | SectionList |
| --- | --- | --- |
| Data | Flat array | Array of sections, each with `data` |
| Headers | One list header | A header for each section |
| Use for | Simple lists | Grouped lists (contacts, settings, menus) |

---

## Summary

- `SectionList` = list split into **sections** with headers.
- Each section needs a **`data`** array.
- Use `renderItem` for items and `renderSectionHeader` for headers.
- Lazy like `FlatList`, so it is fast for long lists.
