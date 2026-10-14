# AsyncStorage

**AsyncStorage** saves small data **on the device**, so it stays after the app closes. It is like `localStorage` on the web, but **async** (returns Promises).

Use it for: language, theme, onboarding seen, small settings, cached data.

---

## Install

```bash
npm install @react-native-async-storage/async-storage
cd ios && pod install && cd ..
```

---

## Basic methods

```tsx
import AsyncStorage from '@react-native-async-storage/async-storage';

// Save
await AsyncStorage.setItem('language', 'ar');

// Read (returns null if missing)
const language = await AsyncStorage.getItem('language');

// Delete
await AsyncStorage.removeItem('language');

// Delete everything
await AsyncStorage.clear();
```

---

## Objects and arrays

AsyncStorage stores **strings only**. Use `JSON.stringify` to save and `JSON.parse` to read.

```tsx
const user = { name: 'Sara', age: 25 };

await AsyncStorage.setItem('user', JSON.stringify(user));

const saved = await AsyncStorage.getItem('user');
const parsed = saved ? JSON.parse(saved) : null;
```

---

## Example: remember the theme

```tsx
function useSavedTheme() {
  const [theme, setTheme] = useState<'light' | 'dark'>('light');

  // Load once on start
  useEffect(() => {
    AsyncStorage.getItem('theme').then(value => {
      if (value === 'light' || value === 'dark') setTheme(value);
    });
  }, []);

  // Save when it changes
  const changeTheme = async (value: 'light' | 'dark') => {
    setTheme(value);
    await AsyncStorage.setItem('theme', value);
  };

  return { theme, changeTheme };
}
```

---

## Why is AsyncStorage not safe for tokens?

AsyncStorage saves data as **plain text**, with **no encryption**:

- **Android:** inside a SQLite database file in the app's data folder.
- **iOS:** inside normal files in the app's folder.

Anyone who can reach those files can read your token directly:

| Risk | How |
| --- | --- |
| **Rooted / jailbroken phone** | Any app or person with root access can open the app's files |
| **Device backups** | Files can end up in phone or computer backups, still readable |
| **Malware** | Malicious apps with elevated access can read the files |
| **Lost or stolen phone** | Someone can pull the files with debugging tools |

A stolen token lets someone **act as the user** without a password.

## Things to know

- Always use **`await`** (or `.then`). Every method is async.
- Wrap in **`try/catch`** in real apps. Reading or writing can fail.
- **Not encrypted.** Do **not** store passwords or auth tokens here.
- Meant for **small** data. For large or complex data, use a database (SQLite, MMKV, WatermelonDB).
- With **Zustand**, use `persist` + AsyncStorage to save a store automatically.

---

## Summary

- AsyncStorage = save small data on the device.
- `setItem`, `getItem`, `removeItem`, all **async**.
- Strings only: use `JSON.stringify` / `JSON.parse` for objects.
- Not secure: use **Keychain** for tokens and passwords.
