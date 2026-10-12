# Functional Components, Props vs State

## In one sentence

A **component** is a function that returns UI. **Props** are data passed **in** from the parent. **State** is data the component **owns and changes** itself.

---

## 1. Functional components

A functional component is a plain JavaScript function that returns JSX.

```jsx
import { View, Text } from 'react-native';

function Greeting() {
  return (
    <View>
      <Text>Hello!</Text>
    </View>
  );
}

export default Greeting;
```

Use it like a tag:

```jsx
<Greeting />
```

**Rules:**

- The name must start with a **capital letter** (`Greeting`, not `greeting`).
- It must return **one** root element. Wrap many elements in a `View` or a fragment `<>...</>`.
- Arrow functions work too: `const Greeting = () => <Text>Hello!</Text>;`

**Why functional, not class components?**
Older React used classes (`class Greeting extends React.Component`). Today, functional components with **hooks** (`useState`, `useEffect`) are the standard. They are shorter and easier to read.

---

## 2. Props

**Props** (properties) are inputs passed from a parent to a child, like function arguments.

```jsx
function Greeting({ name, age }) {
  return <Text>Hello {name}, you are {age}</Text>;
}

// Parent
<Greeting name="Asaad" age={25} />
<Greeting name="Sara" age={30} />
```

**Notes:**

- Props are **read-only**. A child must never change its props.
- Strings use quotes (`name="Asaad"`). Everything else uses braces (`age={25}`, `isActive={true}`, `onPress={handlePress}`).
- You can pass anything: strings, numbers, objects, arrays, functions, even other components.
- Give defaults with destructuring: `function Greeting({ name = 'Guest' })`.

### The `children` prop

Whatever you put between the tags arrives as `children`.

```jsx
function Card({ children }) {
  return <View style={{ padding: 16, borderWidth: 1 }}>{children}</View>;
}

<Card>
  <Text>Inside the card</Text>
</Card>
```

### Passing functions (child talks to parent)

Data flows **down** through props. To send something **up**, the parent passes a function, and the child calls it.

```jsx
function MyButton({ label, onPress }) {
  return (
    <Pressable onPress={onPress}>
      <Text>{label}</Text>
    </Pressable>
  );
}

// Parent
<MyButton label="Save" onPress={() => console.log('saved')} />
```

---

## 3. State

**State** is data a component remembers and can change. When state changes, React **re-renders** the component with the new value.

```jsx
import { useState } from 'react';
import { View, Text, Pressable } from 'react-native';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <View>
      <Text>Count: {count}</Text>
      <Pressable onPress={() => setCount(count + 1)}>
        <Text>Add</Text>
      </Pressable>
    </View>
  );
}
```

`useState(0)` returns two things:

| Part | Meaning |
| --- | --- |
| `count` | The current value |
| `setCount` | The function that updates it |
| `0` | The starting value |

**Rules:**

- **Never change state directly.** `count = 5` does nothing. Always use `setCount(5)`.
- **Updating from the old value?** Use the function form: `setCount(prev => prev + 1)`. It is safe when updates happen quickly.
- **Objects and arrays:** make a new copy, do not edit the old one.

  ```jsx
  setUser({ ...user, name: 'Sara' });   // object
  setItems([...items, newItem]);         // array
  ```

- **Call hooks at the top level** of the component. Not inside `if`, loops or nested functions.
- Each component instance has **its own** state. Two `<Counter />`s count separately.

---

## 4. Props vs State

| | Props | State |
| --- | --- | --- |
| Comes from | The parent | The component itself |
| Can the component change it? | No (read-only) | Yes (with the setter) |
| Purpose | Configure a component from outside | Remember things that change over time |
| Causes re-render when it changes? | Yes | Yes |
| Example | `name`, `label`, `onPress` | Input text, counter, is modal open |

**Easy way to decide:**

- Does the parent give it to me? **Props.**
- Does it change because of the user or time, and only I care? **State.**
- Can it be calculated from other props or state? **Neither.** Just compute it during render.

---

## 5. Putting it together: lifting state up

When two components need the same data, keep the state in their **common parent** and pass it down as props.

```jsx
function NameInput({ value, onChange }) {
  return (
    <TextInput
      value={value}
      onChangeText={onChange}
      style={{ borderWidth: 1, padding: 8 }}
    />
  );
}

function NamePreview({ name }) {
  return <Text>Hello, {name || 'stranger'}!</Text>;
}

function Screen() {
  const [name, setName] = useState('');   // state lives here

  return (
    <View style={{ padding: 16 }}>
      <NameInput value={name} onChange={setName} />   {/* props down */}
      <NamePreview name={name} />                     {/* props down */}
    </View>
  );
}
```

- `Screen` **owns** the state.
- `NameInput` gets the value and a function to change it.
- `NamePreview` only reads the value.

This is the main pattern in React: **state lives up, props flow down, events flow up.**

---

## 6. Notes

> "A functional component is a function that returns JSX. Props are inputs from the parent; they are read-only, like function arguments. State is data the component owns and updates with a setter from `useState`; changing it re-renders the component. If the parent controls it, it's a prop. If the component changes it itself, it's state. When siblings need the same data, lift the state to their parent and pass it down as props."
