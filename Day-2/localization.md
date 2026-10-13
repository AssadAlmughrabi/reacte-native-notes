# Localization: i18next, Language Switching, RTL

**Localization** = showing the app in the user's language (English, Arabic...), including **right-to-left (RTL)** layout for languages like Arabic.

| Tool | Job |
| --- | --- |
| `i18next` + `react-i18next` | Translations and switching language |
| `react-native-localize` | Read the device language |
| `I18nManager` (built in) | Turn RTL layout on/off |

---

## Install

```bash
npm install i18next react-i18next react-native-localize
cd ios && pod install && cd ..
```

---

## 1. Translation files

`src/i18n/en.json`:

```json
{
  "welcome": "Welcome",
  "hello": "Hello, {{name}}"
}
```

`src/i18n/ar.json`:

```json
{
  "welcome": "أهلاً بك",
  "hello": "مرحباً، {{name}}"
}
```

Same **keys**, different **text**.

---

## 2. Set up i18next

```ts
// src/i18n/index.ts
import i18n from 'i18next';
import { initReactI18next } from 'react-i18next';
import * as RNLocalize from 'react-native-localize';
import en from './en.json';
import ar from './ar.json';

const deviceLanguage = RNLocalize.getLocales()[0]?.languageCode ?? 'en';

i18n.use(initReactI18next).init({
  resources: {
    en: { translation: en },
    ar: { translation: ar },
  },
  lng: deviceLanguage,       // start with the device language
  fallbackLng: 'en',         // if a key or language is missing
  interpolation: { escapeValue: false },
});

export default i18n;
```

Import it once at the top of `App.tsx`:

```tsx
import './src/i18n';
```

---

## 3. Use translations

```tsx
import { Text } from 'react-native';
import { useTranslation } from 'react-i18next';

function HomeScreen() {
  const { t } = useTranslation();

  return (
    <>
      <Text>{t('welcome')}</Text>
      <Text>{t('hello', { name: 'Sara' })}</Text>
    </>
  );
}
```

- `t('key')` returns the text in the current language.
- `{{name}}` in the file is filled from `{ name: 'Sara' }`.

---

## 4. Language switching

```tsx
import { I18nManager, Pressable, Text } from 'react-native';
import { useTranslation } from 'react-i18next';

function LanguageButton() {
  const { i18n } = useTranslation();

  async function changeLanguage(lang: 'en' | 'ar') {
    await i18n.changeLanguage(lang);

    const isRTL = lang === 'ar';
    if (I18nManager.isRTL !== isRTL) {
      I18nManager.allowRTL(isRTL);
      I18nManager.forceRTL(isRTL);
      // RTL layout needs an app restart (e.g. react-native-restart)
    }
  }

  return (
    <Pressable onPress={() => changeLanguage(i18n.language === 'ar' ? 'en' : 'ar')}>
      <Text>{i18n.language === 'ar' ? 'English' : 'العربية'}</Text>
    </Pressable>
  );
}
```

- **Text** changes right away.
- **RTL layout** changes only after the app **restarts**. Use `react-native-restart` to restart it for the user.
- Save the chosen language (AsyncStorage) and load it on startup, or it resets to the device language.

---

## 5. RTL support

When RTL is on, React Native **flips the layout** automatically: `flexDirection: 'row'` goes right to left.

Write styles that work both ways:

| Avoid | Use |
| --- | --- |
| `marginLeft`, `marginRight` | `marginStart`, `marginEnd` |
| `paddingLeft`, `paddingRight` | `paddingStart`, `paddingEnd` |
| `left`, `right` (position) | `start`, `end` |
| `textAlign: 'left'` | `textAlign: 'auto'` (or leave default) |

```tsx
<View style={{ flexDirection: 'row', paddingStart: 16 }}>
  <Icon name="user" />
  <Text style={{ marginStart: 8 }}>{t('welcome')}</Text>
</View>
```

**Icons with direction** (arrows, back buttons) must be flipped:

```tsx
<Icon name="arrow-forward" style={{ transform: [{ scaleX: I18nManager.isRTL ? -1 : 1 }] }} />
```

Check RTL anywhere with `I18nManager.isRTL`.

---

## Summary

- **i18next**: translation files with the same keys, read with `t('key')`.
- **Device language**: `react-native-localize`, with `fallbackLng: 'en'`.
- **Switch language**: `i18n.changeLanguage('ar')`. Save it so it stays.
- **RTL**: `I18nManager.forceRTL(true)` + **restart** the app.
- Use **`start` / `end`** styles instead of `left` / `right`, and flip direction icons.
