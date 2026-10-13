# Context API in React Native

**Context** shares data with many screens and components **without passing props** through every level.

Use it for: logged-in user, theme, language, cart.

---

## 1. Create the context and Provider

```tsx
// src/context/ThemeContext.tsx
import { createContext, useContext, useState, ReactNode } from 'react';

type ThemeContextType = {
  theme: 'light' | 'dark';
  toggleTheme: () => void;
};

const ThemeContext = createContext<ThemeContextType | undefined>(undefined);

export function ThemeProvider({ children }: { children: ReactNode }) {
  const [theme, setTheme] = useState<'light' | 'dark'>('light');

  const toggleTheme = () => setTheme(prev => (prev === 'light' ? 'dark' : 'light'));

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

export function useTheme() {
  const context = useContext(ThemeContext);
  if (!context) throw new Error('useTheme must be used inside ThemeProvider');
  return context;
}
```

- `createContext`: makes the context.
- `ThemeProvider`: holds the state and shares it.
- `useTheme`: custom hook to read it (with a clear error if the Provider is missing).

---

## 2. Wrap the app

```tsx
// App.tsx
export default function App() {
  return (
    <ThemeProvider>
      <NavigationContainer>
        <RootNavigator />
      </NavigationContainer>
    </ThemeProvider>
  );
}
```

Put Providers **outside** `NavigationContainer`, so every screen can use them.

---

## 3. Use it in any screen

```tsx
import { View, Text, Pressable } from 'react-native';
import { useTheme } from '../context/ThemeContext';

export default function SettingsScreen() {
  const { theme, toggleTheme } = useTheme();
  const isDark = theme === 'dark';

  return (
    <View style={{ flex: 1, backgroundColor: isDark ? '#000' : '#fff', padding: 16 }}>
      <Text style={{ color: isDark ? '#fff' : '#000' }}>Theme: {theme}</Text>
      <Pressable onPress={toggleTheme}>
        <Text style={{ color: '#2563eb' }}>Toggle theme</Text>
      </Pressable>
    </View>
  );
}
```

When `theme` changes, every screen using `useTheme()` updates.

---

## Tips

- **One context per topic:** `AuthProvider`, `ThemeProvider`, `CartProvider`.
- Every change re-renders **all** components using that context. For big, fast-changing state, use **Zustand** or **Redux Toolkit**.

---

## Summary

- **Create:** `createContext` + a Provider component with `useState`.
- **Wrap:** put the Provider around `NavigationContainer` in `App.tsx`.
- **Use:** a custom hook (`useTheme()`) in any screen.
