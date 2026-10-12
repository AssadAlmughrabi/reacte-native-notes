# StyleSheet

React Native has **no CSS files**. You style components with **JavaScript objects**, usually created with `StyleSheet.create`.

---

## Basic example

```jsx
import { View, Text, StyleSheet } from 'react-native';

function Card() {
  return (
    <View style={styles.card}>
      <Text style={styles.title}>Hello</Text>
    </View>
  );
}

const styles = StyleSheet.create({
  card: {
    padding: 16,
    backgroundColor: '#fff',
    borderRadius: 8,
  },
  title: {
    fontSize: 18,
    fontWeight: 'bold',
    color: '#333',
  },
});
```

- Define styles once at the bottom of the file.
- Use them with the `style` prop: `style={styles.card}`.

---

## Differences from CSS

| CSS | React Native |
| --- | --- |
| `background-color` | `backgroundColor` (camelCase) |
| `16px`, `1rem`, `50%` | `16` (unitless, density-independent pixels). Percentages work as strings: `'50%'` |
| Cascading and inheritance | No cascade. Each component gets only the styles you give it. `Text` inherits only from a parent `Text` |
| Selectors (`.class`, `#id`, `:hover`) | None. You attach styles directly to each element: `style={styles.name}` |
| `display: block / inline / grid` | Flexbox only, plus absolute positioning |
| `flex-direction` defaults to `row` | Defaults to `column` |
| Shorthands like `margin: 10px 20px` | Separate props: `marginVertical: 10`, `marginHorizontal: 20` |
| Media queries | None built in. Use `useWindowDimensions()` |

---

## Combining styles

Pass an **array**. Later styles override earlier ones.

```jsx
<Text style={[styles.title, styles.red]}>Hello</Text>
```

## Conditional styles

```jsx
<View style={[styles.button, isActive && styles.active]} />
```

If `isActive` is `false`, that style is ignored.

## Inline styles

Fine for quick or dynamic values:

```jsx
<View style={{ width: size, height: size }} />
```

For everything else, prefer `StyleSheet.create`.

## Why StyleSheet.create instead of inline objects?

You can write `style={{ padding: 16 }}` inline, but `StyleSheet.create` is better:

- **Validation:** it warns about invalid style properties during development.
- **Organization:** styles live in one place, separate from the JSX, like a component's `.scss` file in Angular.
- **Performance:** inline objects are recreated on every render, while `StyleSheet` objects are created once.

---

## Summary

- Styles are **JS objects**, created with `StyleSheet.create`.
- Property names are **camelCase**, values are **numbers** (no `px`).
- Combine with an **array**: `style={[a, b]}`.
- Layout is **Flexbox**, default direction is **column**.
