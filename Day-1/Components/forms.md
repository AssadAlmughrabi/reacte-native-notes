# Forms: TextInput, KeyboardAvoidingView, Keyboard Dismissal

Three things every mobile form needs:

| Need | Tool |
| --- | --- |
| Let the user type | `TextInput` |
| Keep inputs visible when the keyboard opens | `KeyboardAvoidingView` |
| Close the keyboard | `Keyboard.dismiss()`, tap outside, scroll |

---

## 1. TextInput

```jsx
import { useState } from 'react';
import { TextInput } from 'react-native';

function EmailInput() {
  const [email, setEmail] = useState('');

  return (
    <TextInput
      value={email}
      onChangeText={setEmail}
      placeholder="Email"
      keyboardType="email-address"
      autoCapitalize="none"
      style={{ borderWidth: 1, borderColor: '#ccc', padding: 12, borderRadius: 8 }}
    />
  );
}
```

### Useful props

| Prop | What it does |
| --- | --- |
| `value` + `onChangeText` | Controlled input. `onChangeText` gives the string directly |
| `placeholder` | Gray hint text |
| `keyboardType` | `'default'`, `'numeric'`, `'email-address'`, `'phone-pad'` |
| `secureTextEntry` | Hides text (passwords) |
| `autoCapitalize` | `'none'`, `'sentences'`, `'words'`, `'characters'` |
| `autoCorrect` | Turn autocorrect on/off |
| `multiline` | Multi-line input (like `<textarea>`) |
| `maxLength` | Limit the number of characters |
| `returnKeyType` | Label of the Enter key: `'next'`, `'done'`, `'send'` |
| `onSubmitEditing` | Runs when the user presses Enter |
| `editable={false}` | Read-only |

> `TextInput` has **no default style**. Without a border it is invisible.

### Move to the next input on Enter

Use a `ref` to focus the next field.

```jsx
import { useRef } from 'react';

const passwordRef = useRef(null);

<TextInput
  placeholder="Email"
  returnKeyType="next"
  onSubmitEditing={() => passwordRef.current?.focus()}
/>

<TextInput
  ref={passwordRef}
  placeholder="Password"
  secureTextEntry
  returnKeyType="done"
  onSubmitEditing={handleLogin}
/>
```

---

## 2. KeyboardAvoidingView

The keyboard can **cover** your inputs. `KeyboardAvoidingView` moves or shrinks the content so inputs stay visible.

```jsx
import { KeyboardAvoidingView, Platform } from 'react-native';

<KeyboardAvoidingView
  style={{ flex: 1 }}
  behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
>
  {/* form */}
</KeyboardAvoidingView>
```

| `behavior` | Effect |
| --- | --- |
| `'padding'` | Adds bottom padding equal to the keyboard height. Best on **iOS** |
| `'height'` | Shrinks the view's height. Often used on **Android** |
| `'position'` | Moves the whole content up |

**Notes:**

- Give it `flex: 1` so it fills the screen.
- If you have a header (React Navigation), add `keyboardVerticalOffset={headerHeight}` so the offset is correct.
- For long forms, put a `ScrollView` **inside** it.
- Android often handles the keyboard by itself (`windowSoftInputMode="adjustResize"` in `AndroidManifest.xml`), so test on both platforms.

---

## 3. Keyboard dismissal

### Close it from code

```jsx
import { Keyboard } from 'react-native';

Keyboard.dismiss();
```

Call it, for example, before submitting the form.

### Close it by tapping outside

Wrap the screen in a `Pressable` that calls `Keyboard.dismiss`.

```jsx
<Pressable style={{ flex: 1 }} onPress={Keyboard.dismiss}>
  {/* form */}
</Pressable>
```

### Close it in a ScrollView

```jsx
<ScrollView
  keyboardShouldPersistTaps="handled"
  keyboardDismissMode="on-drag"
>
  {/* form */}
</ScrollView>
```

| Prop | What it does |
| --- | --- |
| `keyboardShouldPersistTaps="handled"` | Buttons work on the **first** tap while the keyboard is open (default needs two taps) |
| `keyboardDismissMode="on-drag"` | Scrolling closes the keyboard |

---

## 4. Full example: login form

```jsx
import { useState, useRef } from 'react';
import {
  View, Text, TextInput, Pressable, ScrollView,
  KeyboardAvoidingView, Keyboard, Platform, StyleSheet,
} from 'react-native';

export default function LoginScreen() {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [error, setError] = useState('');
  const passwordRef = useRef(null);

  function handleLogin() {
    Keyboard.dismiss();
    if (!email || !password) {
      setError('Please fill in all fields');
      return;
    }
    setError('');
    console.log('Login:', email);
  }

  return (
    <KeyboardAvoidingView
      style={{ flex: 1 }}
      behavior={Platform.OS === 'ios' ? 'padding' : 'height'}
    >
      <ScrollView
        contentContainerStyle={styles.container}
        keyboardShouldPersistTaps="handled"
        keyboardDismissMode="on-drag"
      >
        <Text style={styles.title}>Login</Text>

        <TextInput
          style={styles.input}
          placeholder="Email"
          value={email}
          onChangeText={setEmail}
          keyboardType="email-address"
          autoCapitalize="none"
          returnKeyType="next"
          onSubmitEditing={() => passwordRef.current?.focus()}
        />

        <TextInput
          ref={passwordRef}
          style={styles.input}
          placeholder="Password"
          value={password}
          onChangeText={setPassword}
          secureTextEntry
          returnKeyType="done"
          onSubmitEditing={handleLogin}
        />

        {error ? <Text style={styles.error}>{error}</Text> : null}

        <Pressable style={styles.button} onPress={handleLogin}>
          <Text style={styles.buttonText}>Log in</Text>
        </Pressable>
      </ScrollView>
    </KeyboardAvoidingView>
  );
}

const styles = StyleSheet.create({
  container: { flexGrow: 1, justifyContent: 'center', padding: 24, gap: 12 },
  title: { fontSize: 28, fontWeight: 'bold', marginBottom: 12 },
  input: { borderWidth: 1, borderColor: '#ccc', borderRadius: 8, padding: 12 },
  error: { color: 'red' },
  button: { backgroundColor: '#2563eb', padding: 14, borderRadius: 8, alignItems: 'center' },
  buttonText: { color: 'white', fontWeight: 'bold' },
});
```

> For big forms with lots of validation, libraries like **React Hook Form** (with **Zod** or **Yup**) save a lot of code.

---

## Summary

- **`TextInput`** = controlled with `value` + `onChangeText`. Give it a border.
- **`returnKeyType` + `ref.focus()`** = jump to the next field.
- **`KeyboardAvoidingView`** = keeps inputs above the keyboard. `'padding'` on iOS, `'height'` on Android.
- **Dismiss** = `Keyboard.dismiss()`, tap outside, or `keyboardDismissMode="on-drag"`.
- **`keyboardShouldPersistTaps="handled"`** = buttons work on the first tap.
