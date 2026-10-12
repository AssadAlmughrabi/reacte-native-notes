# Auth Navigation (Logged-in and Logged-out Stacks)

Show **different screens** depending on whether the user is logged in:

- **Logged out:** Login, Register
- **Logged in:** Home, Profile

React Navigation does this with a simple `if`: render one group of screens or the other. When the state changes, it switches automatically.

---

## 1. Mock auth state with Context

No real backend yet, just a `useState` shared through Context.

```tsx
// src/context/AuthContext.tsx
import { createContext, useContext, useState } from 'react';

const AuthContext = createContext(null);

export function AuthProvider({ children }) {
  const [isLoggedIn, setIsLoggedIn] = useState(false);

  const login = () => setIsLoggedIn(true);
  const logout = () => setIsLoggedIn(false);

  return (
    <AuthContext.Provider value={{ isLoggedIn, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}

export const useAuth = () => useContext(AuthContext);
```

---

## 2. Switch stacks based on the state

```tsx
// App.tsx
const Stack = createNativeStackNavigator();

function RootNavigator() {
  const { isLoggedIn } = useAuth();

  return (
    <Stack.Navigator>
      {isLoggedIn ? (
        <>
          <Stack.Screen name="Home" component={HomeScreen} />
          <Stack.Screen name="Profile" component={ProfileScreen} />
        </>
      ) : (
        <>
          <Stack.Screen name="Login" component={LoginScreen} />
          <Stack.Screen name="Register" component={RegisterScreen} />
        </>
      )}
    </Stack.Navigator>
  );
}

export default function App() {
  return (
    <AuthProvider>
      <NavigationContainer>
        <RootNavigator />
      </NavigationContainer>
    </AuthProvider>
  );
}
```

---

## 3. Log in and log out

```tsx
function LoginScreen() {
  const { login } = useAuth();
  return <Button title="Log in" onPress={login} />;
}

function HomeScreen() {
  const { logout } = useAuth();
  return <Button title="Log out" onPress={logout} />;
}
```

**Do not** call `navigation.navigate('Home')` after login. Just change the state, and React Navigation shows the right screens by itself.

---

## Why this way?

- After login, the user **cannot go back** to the Login screen (it no longer exists).
- After logout, the logged-in screens are **removed**, so no private data stays in memory.

---

## Summary

- Keep `isLoggedIn` in **Context** (mock with `useState` for now).
- Render **logged-in** or **logged-out** screens with a condition.
- Change the state to log in/out. **No manual navigate.**
- Later, replace the mock with a real API and save the token (e.g. in secure storage).
