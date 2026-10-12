# Flexbox Layout

In React Native, **every `View` is a flex container**. Flexbox is the only layout system (plus absolute positioning).

---

## Differences from web Flexbox

| Web | React Native |
| --- | --- |
| Need `display: flex` | Always on, no need to set it |
| `flex-direction` defaults to `row` | Defaults to **`column`** (top to bottom) |
| `flex: 1` is shorthand for grow/shrink/basis | `flex: 1` means "take the available space" |
| `align-content` defaults to `stretch` | Defaults to `flex-start` |

---

## Main axis and cross axis

- **Main axis:** the direction of `flexDirection`.
- **Cross axis:** the other direction.

| `flexDirection` | Main axis | Cross axis |
| --- | --- | --- |
| `'column'` (default) | Vertical | Horizontal |
| `'row'` | Horizontal | Vertical |

- `justifyContent` aligns on the **main** axis.
- `alignItems` aligns on the **cross** axis.

---

## The main properties

### flexDirection

Which way children are placed.

```jsx
<View style={{ flexDirection: 'row' }}>
```

Values: `'column'` (default), `'row'`, `'column-reverse'`, `'row-reverse'`

### justifyContent (main axis)

```jsx
<View style={{ justifyContent: 'center' }}>
```

Values: `'flex-start'` (default), `'center'`, `'flex-end'`, `'space-between'`, `'space-around'`, `'space-evenly'`

### alignItems (cross axis)

```jsx
<View style={{ alignItems: 'center' }}>
```

Values: `'stretch'` (default), `'flex-start'`, `'center'`, `'flex-end'`, `'baseline'`

### flex

How much of the free space a child takes.

```jsx
<View style={{ flex: 1, flexDirection: 'row' }}>
  <View style={{ flex: 1, backgroundColor: 'red' }} />   {/* 1/3 */}
  <View style={{ flex: 2, backgroundColor: 'blue' }} />  {/* 2/3 */}
</View>
```

- `flex: 1` on the screen's root `View` makes it fill the whole screen.
- Children share space by their `flex` numbers.

### Other useful properties

| Property | What it does |
| --- | --- |
| `alignSelf` | Overrides `alignItems` for one child |
| `flexWrap: 'wrap'` | Moves children to the next line when there is no room |
| `gap`, `rowGap`, `columnGap` | Space between children |
| `position: 'absolute'` | Takes the child out of the flow; place it with `top`, `left`, `right`, `bottom` |

---

## Common layouts

### Center everything

```jsx
<View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
  <Text>Centered</Text>
</View>
```

### Row with items on both ends

```jsx
<View style={{ flexDirection: 'row', justifyContent: 'space-between', alignItems: 'center' }}>
  <Text>Title</Text>
  <Text>Edit</Text>
</View>
```

### Header, content, footer

```jsx
<View style={{ flex: 1 }}>
  <View style={{ height: 60 }} />   {/* header */}
  <View style={{ flex: 1 }} />      {/* content fills the middle */}
  <View style={{ height: 60 }} />   {/* footer */}
</View>
```

### Floating button

```jsx
<Pressable style={{ position: 'absolute', bottom: 20, right: 20 }}>
  <Text>+</Text>
</Pressable>
```

---

## Summary

- Every `View` is flex. Default direction is **column**.
- `justifyContent` = main axis, `alignItems` = cross axis.
- `flex: 1` = fill the available space.
- Use `gap` for spacing and `position: 'absolute'` for overlays.
