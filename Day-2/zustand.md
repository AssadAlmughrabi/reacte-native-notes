# Zustand

**Zustand** is a small library for **global state**. It is simpler than Redux and avoids the extra re-renders of Context.

- No Provider needed
- A store is just a hook
- Components re-render only when the part they use changes

---

## Install

```bash
npm install zustand
```

---

## 1. Create a store

```tsx
// src/store/useCartStore.ts
import { create } from 'zustand';

type CartItem = { id: string; name: string };

type CartState = {
  items: CartItem[];
  addItem: (item: CartItem) => void;
  removeItem: (id: string) => void;
  clear: () => void;
};

export const useCartStore = create<CartState>()(set => ({
  items: [],
  addItem: item => set(state => ({ items: [...state.items, item] })),
  removeItem: id => set(state => ({ items: state.items.filter(i => i.id !== id) })),
  clear: () => set({ items: [] }),
}));
```

- `create` makes the store and returns a **hook**.
- `set` updates the state (it merges, so you only pass what changed).

---

## 2. Use it in any screen

```tsx
import { View, Text, Pressable } from 'react-native';
import { useCartStore } from '../store/useCartStore';

function ProductScreen() {
  const addItem = useCartStore(state => state.addItem);

  return (
    <Pressable onPress={() => addItem({ id: '1', name: 'Phone' })}>
      <Text>Add to cart</Text>
    </Pressable>
  );
}

function CartBadge() {
  const count = useCartStore(state => state.items.length);

  return <Text>Cart: {count}</Text>;
}
```

No Provider, no wrapping `App.tsx`. Just import the hook.

---

## 3. Selectors (important)

Pick **only what you need**:

```tsx
const count = useCartStore(state => state.items.length);   // good
const store = useCartStore();                               // re-renders on every change
```

`CartBadge` re-renders only when `items.length` changes, not on every store change.

---

## 4. Save to storage (optional)

Keep the state after the app closes with `persist` + AsyncStorage:

```bash
npm install @react-native-async-storage/async-storage
cd ios && pod install && cd ..
```

```tsx
import { create } from 'zustand';
import { persist, createJSONStorage } from 'zustand/middleware';
import AsyncStorage from '@react-native-async-storage/async-storage';

export const useCartStore = create<CartState>()(
  persist(
    set => ({
      items: [],
      addItem: item => set(state => ({ items: [...state.items, item] })),
      removeItem: id => set(state => ({ items: state.items.filter(i => i.id !== id) })),
      clear: () => set({ items: [] }),
    }),
    {
      name: 'cart-storage',
      storage: createJSONStorage(() => AsyncStorage),
    }
  )
);
```

---

## Context vs Zustand

| | Context | Zustand |
| --- | --- | --- |
| Setup | Provider + hook | Just a hook |
| Re-renders | All consumers on any change | Only components using the changed part |
| Best for | Small, rarely changing data (theme, auth) | Bigger or often changing state (cart, filters) |

---

## Summary

- `create` makes a store that is a **hook**.
- Update with `set`. No Provider needed.
- Use **selectors**: `useStore(state => state.part)`.
- Add `persist` + AsyncStorage to save state on the device.
