# Network State with NetInfo

**NetInfo** tells you if the device is **online or offline**, and what kind of connection it has (Wi-Fi, cellular).

Use it to: show an "offline" banner, pause requests, retry when the connection comes back.

---

## Install

```bash
npm install @react-native-community/netinfo
cd ios && pod install && cd ..
```

Rebuild the app after installing.

---

## 1. useNetInfo hook (easiest)

Re-renders the component when the network changes.

```tsx
import { useNetInfo } from '@react-native-community/netinfo';
import { View, Text } from 'react-native';

function OfflineBanner() {
  const { isConnected } = useNetInfo();

  if (isConnected !== false) return null;

  return (
    <View style={{ backgroundColor: '#dc2626', padding: 8 }}>
      <Text style={{ color: 'white', textAlign: 'center' }}>No internet connection</Text>
    </View>
  );
}
```

> `isConnected` is **`null`** at first (unknown), so check `=== false` for offline.

---

## 2. Check once

```ts
import NetInfo from '@react-native-community/netinfo';

const state = await NetInfo.fetch();
if (!state.isConnected) {
  showMessage('You are offline');
}
```

---

## 3. Listen for changes

```ts
useEffect(() => {
  const unsubscribe = NetInfo.addEventListener(state => {
    console.log('Online?', state.isConnected);
  });

  return () => unsubscribe();   // cleanup
}, []);
```

---

## Useful fields

| Field | Meaning |
| --- | --- |
| `isConnected` | Connected to a network (Wi-Fi, cellular) |
| `isInternetReachable` | The internet **actually works** (not just connected to Wi-Fi) |
| `type` | `'wifi'`, `'cellular'`, `'none'`, `'unknown'` |
| `details.isConnectionExpensive` | Mobile data (avoid big downloads) |

> Connected to Wi-Fi **does not** mean internet works (e.g. café login pages). Use `isInternetReachable` for that.

---

## Summary

- NetInfo = online/offline status + connection type.
- **`useNetInfo()`** in components, **`NetInfo.fetch()`** to check once, **`addEventListener`** to listen.
- Check `isConnected === false` for offline.
- Use `isInternetReachable` to know if the internet really works.
