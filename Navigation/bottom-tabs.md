# Bottom Tabs

**Bottom tabs** show a bar at the bottom of the screen with buttons (tabs). Tapping a tab switches to its screen. Think Instagram: Home, Search, Profile.

---

## Install

```bash
npm install @react-navigation/bottom-tabs
```

(Needs the core setup first: see `navigation-installation.md`.)

---

## Basic setup

```jsx
import { NavigationContainer } from '@react-navigation/native';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';

const Tab = createBottomTabNavigator();

export default function App() {
  return (
    <NavigationContainer>
      <Tab.Navigator>
        <Tab.Screen name="Home" component={HomeScreen} />
        <Tab.Screen name="Search" component={SearchScreen} />
        <Tab.Screen name="Profile" component={ProfileScreen} />
      </Tab.Navigator>
    </NavigationContainer>
  );
}
```

---

## Add icons

Use `tabBarIcon`. It gives you `focused`, `color` and `size`.

```jsx
import Ionicons from 'react-native-vector-icons/Ionicons';

<Tab.Navigator
  screenOptions={({ route }) => ({
    tabBarIcon: ({ focused, color, size }) => {
      const icons = {
        Home: focused ? 'home' : 'home-outline',
        Search: focused ? 'search' : 'search-outline',
        Profile: focused ? 'person' : 'person-outline',
      };
      return <Ionicons name={icons[route.name]} size={size} color={color} />;
    },
    tabBarActiveTintColor: '#2563eb',
    tabBarInactiveTintColor: 'gray',
  })}
>
```

> `react-native-vector-icons` needs its own native setup in a CLI project. Follow its README.

---

## Useful options

| Option | What it does |
| --- | --- |
| `tabBarIcon` | Icon for the tab |
| `tabBarLabel` | Text under the icon |
| `tabBarActiveTintColor` | Color of the selected tab |
| `tabBarInactiveTintColor` | Color of the other tabs |
| `tabBarBadge` | Small badge, like `3` for notifications |
| `headerShown: false` | Hide the header |
| `tabBarStyle` | Style the bar (background, height) |

Example badge:

```jsx
<Tab.Screen name="Inbox" component={InboxScreen} options={{ tabBarBadge: 3 }} />
```

---

## Tabs + stack together

Usually each tab has its own stack, so you can open details screens inside a tab:

```jsx
const Stack = createNativeStackNavigator();

function HomeStack() {
  return (
    <Stack.Navigator>
      <Stack.Screen name="Feed" component={FeedScreen} />
      <Stack.Screen name="Post" component={PostScreen} />
    </Stack.Navigator>
  );
}

<Tab.Navigator>
  <Tab.Screen name="Home" component={HomeStack} options={{ headerShown: false }} />
  <Tab.Screen name="Profile" component={ProfileScreen} />
</Tab.Navigator>
```

`headerShown: false` on the tab avoids showing **two** headers.

---

## Summary

- Bottom tabs = bar at the bottom to switch between main screens.
- `Tab.Navigator` + `Tab.Screen`.
- Icons with `tabBarIcon`, colors with `tabBarActiveTintColor`.
- Put a **stack inside a tab** for detail screens.
- Tabs are **lazy**: a screen renders the first time you open it.
