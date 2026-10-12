# Typing Navigation with TypeScript

TypeScript checks your navigation: wrong screen names or missing params become **errors**, and you get **autocomplete**.

---

## 1. Define a param list

List every screen and its params. Use `undefined` for no params.

```ts
export type RootStackParamList = {
  Home: undefined;
  Details: { id: number };
};

const Stack = createNativeStackNavigator<RootStackParamList>();
```

---

## 2. Type a screen

```tsx
import type { NativeStackScreenProps } from '@react-navigation/native-stack';

type Props = NativeStackScreenProps<RootStackParamList, 'Details'>;

function DetailsScreen({ route }: Props) {
  const { id } = route.params;   // id is a number
  return <Text>Item {id}</Text>;
}
```

---

## 3. What TypeScript catches

```tsx
navigation.navigate('Details', { id: 1 });     // OK
navigation.navigate('Details');                // Error: missing id
navigation.navigate('Detail', { id: 1 });      // Error: wrong screen name
```

---

## Summary

- Make a **ParamList** type: screen name and its params.
- Pass it to `createNativeStackNavigator<ParamList>()`.
- Type screens with `NativeStackScreenProps<ParamList, 'Screen'>`.
