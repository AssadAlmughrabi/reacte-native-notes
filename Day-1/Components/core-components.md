# Core Components

**Core components** are the built-in UI building blocks that React Native ships with.

React Native maps each one to the platform's real native view. For example, a `<View />` becomes a `UIView` on iOS and an `android.view.View` on Android.

| Component | Web equivalent | Used for |
| --- | --- | --- |
| [`View`](#1-view) | `<div>` | Layout and grouping |
| [`Text`](#2-text) | `<span>`, `<p>` | Showing text |
| [`Image`](#3-image) | `<img>` | Showing images |
| [`ScrollView`](#4-scrollview) | scrolling `<div>` | Scrollable content |
| [`TextInput`](#5-textinput) | `<input>`, `<textarea>` | Typing text |
| [`Pressable`](#6-pressable) | `<button>` | Anything tappable |
| [`Modal`](#7-modal) | dialog | Overlay on top of the screen |

---

## 1. View

The basic container, used for layout, grouping and styling.

```jsx
import { View } from 'react-native';

<View style={{ flexDirection: 'row', padding: 16, backgroundColor: '#eee' }}>
  {/* children */}
</View>
```

**Different from the web:**

- Every `View` is a flex container by default, and `flexDirection` defaults to **`column`**, not `row`.
- There is no `block` or `inline`. Layout is Flexbox only (plus absolute positioning).
- You cannot put raw text inside a `View`. `<View>Hello</View>` crashes. Text must go inside `<Text>`.
- It does not scroll. Content that overflows is cut off.

---

## 2. Text

The only place text can live. Every string on the screen must be wrapped in `<Text>`.

```jsx
<Text style={{ fontSize: 18, fontWeight: 'bold' }}>
  Hello <Text style={{ color: 'red' }}>world</Text>
</Text>
```

**Notes:**

- Nested `Text` behaves like an inline `<span>`, and inherits styles from its parent `Text`.
- Style inheritance only works `Text` to `Text`. Unlike CSS, a `View` cannot set the font for its children.
- `numberOfLines={2}` cuts long text with an ellipsis (`...`). Handy in lists.
- It supports `onPress`, but for real buttons use `Pressable`.

---

## 3. Image

The `<img>` of React Native.

```jsx
// Local image
<Image source={require('./assets/logo.png')} />

// Remote image (must have a size)
<Image
  source={{ uri: 'https://example.com/photo.jpg' }}
  style={{ width: 200, height: 200 }}
  resizeMode="cover"
/>
```

**Notes:**

- Remote images render at 0×0 unless you give them a size.
- `resizeMode` (`cover`, `contain`, `stretch`, `center`) works like CSS `object-fit`.
- For a background image with content on top, use `ImageBackground`.
- `require()` needs a fixed string. You cannot build the path dynamically.

---

## 4. ScrollView

A scrollable container. A plain `View` does not scroll, so wrap the content in a `ScrollView`.

```jsx
<ScrollView contentContainerStyle={{ padding: 16 }}>
  <Text>Lots of content...</Text>
</ScrollView>

<ScrollView horizontal showsHorizontalScrollIndicator={false}>
  {/* carousel-style row */}
</ScrollView>
```

**Notes:**

- It renders **all** its children at once, even the ones off-screen. Fine for a form or a details page, bad for long or dynamic lists.
- **Rule:** `ScrollView` for a small, known amount of content. `FlatList` for data arrays (it renders lazily).
- `contentContainerStyle` styles the inner content (padding, centering). Plain `style` applies to the outer frame.

---

## 5. TextInput

The `<input>` of React Native.

```jsx
const [name, setName] = useState('');

<TextInput
  value={name}
  onChangeText={setName}
  placeholder="Your name"
  keyboardType="default"
  style={{ borderWidth: 1, padding: 8, borderRadius: 6 }}
/>
```

**Notes:**

- `onChangeText` gives you the new string directly. There is no `e.target.value`.
- Usually controlled with `useState`.
- `keyboardType` picks which keyboard appears (`numeric`, `email-address`, `phone-pad`).
- `multiline` turns it into a textarea.
- It has no default styling, so it is invisible until you give it a border.
- The keyboard can cover inputs. You will eventually need `KeyboardAvoidingView` for that.

---

## 6. Pressable

Anything tappable. The modern way to handle taps. It replaces the older `TouchableOpacity` and similar components.

```jsx
<Pressable
  onPress={() => console.log('tapped')}
  onLongPress={() => console.log('held')}
  style={({ pressed }) => [
    { padding: 12, backgroundColor: 'blue', borderRadius: 8 },
    pressed && { opacity: 0.6 },
  ]}
>
  <Text style={{ color: 'white' }}>Submit</Text>
</Pressable>
```

**Notes:**

- It is a wrapper, so you can put anything inside: `Text`, an icon, a whole card.
- `style` can be a function that receives `{ pressed }`. That is how you show press feedback, since there is no CSS `:active`.
- It handles `onPressIn`, `onPressOut` and `onLongPress`. `hitSlop` makes the tap area bigger.
- There is also a built-in `<Button>`, but you can barely style it, so most apps use `Pressable`.

---

## 7. Modal

An overlay on top of the current screen.

```jsx
const [visible, setVisible] = useState(false);

<Modal
  visible={visible}
  animationType="slide"                     // 'none' | 'slide' | 'fade'
  transparent={true}                        // lets you build a dimmed backdrop yourself
  onRequestClose={() => setVisible(false)}  // Android back button
>
  <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center',
                 backgroundColor: 'rgba(0,0,0,0.5)' }}>
    <View style={{ backgroundColor: 'white', padding: 20, borderRadius: 12 }}>
      <Text>Are you sure?</Text>
      <Pressable onPress={() => setVisible(false)}><Text>Close</Text></Pressable>
    </View>
  </View>
</Modal>
```

**Notes:**

- Whether it shows is controlled by state through the `visible` prop.
- `onRequestClose` is required on Android. It runs when the user presses the back button.
- `transparent={false}` (the default) covers the whole screen. With `true`, you build the backdrop and the centered card yourself, like in the example.
- Use it for dialogs and sheets. Full screens belong in navigation (React Navigation), not in a `Modal`.
