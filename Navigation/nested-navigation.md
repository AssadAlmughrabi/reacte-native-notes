# Nested Navigation

**Nested navigation** means putting a navigator **inside a screen** of another navigator. Real apps almost always do this, for example tabs inside a stack.

---

## Common pattern: tabs inside a stack

The root **stack** holds the **tabs** plus screens that should cover the tabs (details, login, modals).

```jsx
const Stack = createNativeStackNavigator();
const Tab = createBottomTabNavigator();

function HomeTabs() {
  return (
    <Tab.Navigator>
      <Tab.Screen name="Feed" component={FeedScreen} />
      <Tab.Screen name="Profile" component={ProfileScreen} />
    </Tab.Navigator>
  );
}

export default function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator>
        <Stack.Screen name="Home" component={HomeTabs} options={{ headerShown: false }} />
        <Stack.Screen name="Details" component={DetailsScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}
```

```text
Stack
├── Home (Tabs)
│   ├── Feed
│   └── Profile
└── Details      ← opens on top of the tabs, tab bar hidden
```

- `headerShown: false` on `Home` avoids **two** headers (stack + tabs).
- `Details` hides the tab bar because it is outside the tabs.

---

## Navigating between nested screens

**To a screen in the parent:** just use its name. React Navigation looks up through the parents.

```jsx
// From FeedScreen (inside tabs)
navigation.navigate('Details', { id: 1 });
```

**To a screen inside a nested navigator:** name the navigator, then the screen.

```jsx
navigation.navigate('Home', {
  screen: 'Profile',
  params: { userId: 5 },
});
```

---

## Things to know

- Each navigator keeps its **own history**. `goBack()` goes back inside the current navigator first.
- `navigation.getParent()` gives you the parent navigator's `navigation`.
- Options like `title` only affect the navigator the screen belongs to.
- Keep nesting **shallow**. Too many levels makes the app slow and hard to follow.

---

## Summary

- Nested navigation = a navigator inside a screen of another navigator.
- Most common: **root stack** with **tabs** inside, and detail screens on top.
- Navigate into a nested screen with `navigate('Parent', { screen: 'Child', params })`.
- Hide the extra header with `headerShown: false`.
