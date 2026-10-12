# Drawer

A **drawer** is a side menu that slides in from the left (or right). Open it by swiping from the edge or tapping the ☰ button in the header.

---

## Install

```bash
npm install @react-navigation/drawer
npm install react-native-gesture-handler react-native-reanimated
cd ios && pod install && cd ..
```

- **Gesture handler:** wrap the app in `GestureHandlerRootView` (see `navigation-installation.md`).
- **Reanimated:** add its Babel plugin to `babel.config.js` (follow the Reanimated docs for your version), then restart Metro with `npm start -- --reset-cache`.

---

## Basic setup

```jsx
import { GestureHandlerRootView } from 'react-native-gesture-handler';
import { NavigationContainer } from '@react-navigation/native';
import { createDrawerNavigator } from '@react-navigation/drawer';

const Drawer = createDrawerNavigator();

export default function App() {
  return (
    <GestureHandlerRootView style={{ flex: 1 }}>
      <NavigationContainer>
        <Drawer.Navigator>
          <Drawer.Screen name="Home" component={HomeScreen} />
          <Drawer.Screen name="Settings" component={SettingsScreen} />
        </Drawer.Navigator>
      </NavigationContainer>
    </GestureHandlerRootView>
  );
}
```

The header gets a ☰ button automatically.

---

## Open and close from code

```jsx
navigation.openDrawer();
navigation.closeDrawer();
navigation.toggleDrawer();
```

---

## Useful options

| Option | What it does |
| --- | --- |
| `drawerPosition: 'right'` | Open from the right |
| `drawerType` | `'front'`, `'slide'`, `'back'`, `'permanent'` |
| `drawerActiveTintColor` | Color of the selected item |
| `drawerIcon` | Icon next to each item |
| `drawerStyle` | Style the drawer (width, background) |
| `swipeEnabled: false` | Turn off swipe to open |

```jsx
<Drawer.Navigator screenOptions={{ drawerPosition: 'right', drawerType: 'slide' }}>
```

---

## Summary

- Drawer = side menu that slides in.
- Needs **gesture-handler** and **reanimated**.
- `Drawer.Navigator` + `Drawer.Screen`.
- Control it with `openDrawer`, `closeDrawer`, `toggleDrawer`.
