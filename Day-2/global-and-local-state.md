# Global and Local State

**State** is data that can change and updates the UI when it does. There are two kinds, based on **who needs it**.

| | Local state | Global state |
| --- | --- | --- |
| Who uses it | One component (and its children) | Many screens across the app |
| Tool | `useState` | Context, Zustand, Redux |
| Examples | Input text, is modal open, selected tab | Logged-in user, theme, cart, language |

---

## Local state

Lives **inside one component** with `useState`.

```jsx
function SearchBar() {
  const [query, setQuery] = useState('');

  return <TextInput value={query} onChangeText={setQuery} />;
}
```

Nobody else needs `query`, so keep it local.

---

## The problem: prop drilling

When many components need the same data, you pass it down through every level, even components that do not use it.

```text
App (user)
└── HomeScreen (user)        ← does not use it, just passes it
    └── Header (user)        ← does not use it, just passes it
        └── Avatar (user)    ← finally uses it
```

Global state fixes this.

---

## Global state with Context

Put the data in a **Provider** at the top. Any component can read it directly.

```jsx
import { createContext, useContext, useState } from 'react';

const UserContext = createContext(null);

export function UserProvider({ children }) {
  const [user, setUser] = useState(null);

  return (
    <UserContext.Provider value={{ user, setUser }}>
      {children}
    </UserContext.Provider>
  );
}

export const useUser = () => useContext(UserContext);
```

```jsx
// App.tsx
<UserProvider>
  <NavigationContainer>...</NavigationContainer>
</UserProvider>

// Any component
function Avatar() {
  const { user } = useUser();
  return <Text>{user?.name}</Text>;
}
```

> For big apps with lots of global data, libraries like **Zustand** or **Redux Toolkit** are easier to scale than Context.

---

## Which one to use?

Ask: **who needs this data?**

- Only this component? **Local** (`useState`).
- This component and a few children? **Local**, pass it with props.
- Many screens, far apart? **Global** (Context, Zustand, Redux).

**Start local.** Move to global only when you need to.

---

## Summary

- **Local state** = `useState`, used by one component.
- **Global state** = shared across the app (Context, Zustand, Redux).
- Global state avoids **prop drilling**.
- Start local, go global only when many screens need the data.
