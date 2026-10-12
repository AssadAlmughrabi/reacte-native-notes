# Native Stack

A **stack navigator** works like a stack of cards: opening a screen puts it **on top**, going back **removes** it.

The **native stack** (`@react-navigation/native-stack`) uses the real native navigation of each platform (`UINavigationController` on iOS, `Fragment` on Android). It is fast and looks native.

---

## Install

```bash
npm install @react-navigation/native-stack
```

(Needs the core setup first: see `navigation-installation.md`.)

---

## Basic setup

```jsx
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';

const Stack = createNativeStackNavigator();

export default function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator initialRouteName="Home">
        <Stack.Screen name="Home" component={HomeScreen} />
        <Stack.Screen name="Details" component={DetailsScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}
```

- `Stack.Navigator` holds the screens.
- `Stack.Screen` registers a screen with a `name`.
- `initialRouteName` is the first screen shown.

---

## Navigate between screens

```jsx
function HomeScreen({ navigation }) {
  return (
    <Pressable onPress={() => navigation.navigate('Details', { id: 42 })}>
      <Text>Go to details</Text>
    </Pressable>
  );
}

function DetailsScreen({ route, navigation }) {
  const { id } = route.params;

  return (
    <View>
      <Text>Item {id}</Text>
      <Pressable onPress={() => navigation.goBack()}>
        <Text>Back</Text>
      </Pressable>
    </View>
  );
}
```

| Method | What it does |
| --- | --- |
| `navigate('Name', params)` | Go to a screen (reuses it if already in the stack) |
| `push('Name', params)` | Always add a new copy on top |
| `goBack()` | Go back one screen |
| `popToTop()` | Go back to the first screen |
| `replace('Name')` | Swap the current screen (e.g. after login) |

- Read params with **`route.params`**.
- In nested components, use the hooks **`useNavigation()`** and **`useRoute()`**.

---

## Header options

```jsx
<Stack.Screen
  name="Details"
  component={DetailsScreen}
  options={{
    title: 'Item Details',
    headerStyle: { backgroundColor: '#2563eb' },
    headerTintColor: 'white',
  }}
/>
```

Useful options:

| Option | What it does |
| --- | --- |
| `title` | Header text |
| `headerShown: false` | Hide the header |
| `headerRight` | A button on the right |
| `presentation: 'modal'` | Open as a modal (slides from the bottom on iOS) |
| `animation` | `'slide_from_right'`, `'fade'`, `'none'`... |

Set options for **all** screens with `screenOptions` on the navigator:

```jsx
<Stack.Navigator screenOptions={{ headerShown: false }}>
```

Change the title from inside a screen:

```jsx
navigation.setOptions({ title: 'New title' });
```

---

## Summary

- Native stack = screens on top of each other, using **real native navigation**.
- `Stack.Navigator` + `Stack.Screen` with a `name`.
- Move with `navigate`, `push`, `goBack`. Pass data with params, read with `route.params`.
- Customize the header with `options` and `screenOptions`.
