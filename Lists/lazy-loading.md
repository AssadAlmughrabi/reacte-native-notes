# Lazy Loading

**Lazy loading** means loading or rendering something **only when it is needed**, not all at once. This makes the app start faster and use less memory.

In React Native, it shows up in four places:

| Where | What is lazy |
| --- | --- |
| Lists | Only on-screen rows are rendered (`FlatList`) |
| Data | Load more items as the user scrolls (pagination) |
| Screens | A screen renders only when you first open it |
| Components | Load a component's code only when it is shown (`React.lazy`) |

---

## 1. Lazy rendering in lists

`FlatList` is lazy by default: it renders only the rows near the screen. `ScrollView` + `map()` renders **everything**.

```jsx
<FlatList
  data={items}
  keyExtractor={item => item.id}
  renderItem={({ item }) => <Row item={item} />}
  initialNumToRender={10}
  maxToRenderPerBatch={10}
  windowSize={5}
/>
```

| Prop | What it does |
| --- | --- |
| `initialNumToRender` | Rows to render on first load |
| `maxToRenderPerBatch` | Rows to render in each batch while scrolling |
| `windowSize` | How many screens of rows to keep rendered (default 21). Smaller = less memory |

Make rows cheaper with `React.memo`, so they do not re-render when nothing changed:

```jsx
const Row = React.memo(function Row({ item }) {
  return <Text>{item.name}</Text>;
});
```

---

## 2. Lazy loading data (infinite scroll)

Load a small page first, then load the next page when the user reaches the end.

```jsx
import { useState, useEffect } from 'react';
import { FlatList, Text, ActivityIndicator } from 'react-native';

function PostList() {
  const [posts, setPosts] = useState([]);
  const [page, setPage] = useState(1);
  const [loading, setLoading] = useState(false);
  const [hasMore, setHasMore] = useState(true);

  async function loadMore() {
    if (loading || !hasMore) return;
    setLoading(true);

    const res = await fetch(`https://api.example.com/posts?page=${page}&limit=20`);
    const data = await res.json();

    setPosts(prev => [...prev, ...data]);
    setHasMore(data.length === 20);
    setPage(prev => prev + 1);
    setLoading(false);
  }

  useEffect(() => {
    loadMore();
  }, []);

  return (
    <FlatList
      data={posts}
      keyExtractor={item => String(item.id)}
      renderItem={({ item }) => <Text>{item.title}</Text>}
      onEndReached={loadMore}
      onEndReachedThreshold={0.5}
      ListFooterComponent={loading ? <ActivityIndicator /> : null}
    />
  );
}
```

- `onEndReached` runs when the user is near the end.
- `onEndReachedThreshold={0.5}` = start loading half a screen before the end.
- `loading` and `hasMore` stop duplicate or useless requests.

> Libraries like **React Query** (`useInfiniteQuery`) handle pages, caching and loading states for you.

---

## 3. Lazy screens (React Navigation)

- **Bottom tabs:** lazy by default. A tab screen renders only the first time you open it.
- **Stack:** a screen renders only when you navigate to it.

```jsx
<Tab.Navigator screenOptions={{ lazy: true }}>   {/* true is the default */}
  <Tab.Screen name="Home" component={HomeScreen} />
  <Tab.Screen name="Profile" component={ProfileScreen} />
</Tab.Navigator>
```

Set `lazy: false` only if you want all tabs ready immediately.

---

## 4. Lazy components (React.lazy)

Load a heavy component only when it is shown.

```jsx
import { lazy, Suspense } from 'react';
import { ActivityIndicator } from 'react-native';

const Chart = lazy(() => import('./Chart'));

function Stats() {
  return (
    <Suspense fallback={<ActivityIndicator />}>
      <Chart />
    </Suspense>
  );
}
```

- `lazy()` loads the component the first time it renders.
- `Suspense` shows the `fallback` while it loads.

> **Note:** on the web this splits the code into smaller files. In React Native, all JS is usually in **one bundle** on the device, so the gain is smaller: it delays **running** the module's code, which can still speed up app startup.

---

## 5. Lazy images

- `Image` loads remote images when it renders. Inside a `FlatList`, off-screen images are not loaded yet.
- For many images, use **`expo-image`** or **`react-native-fast-image`**: they add caching and smoother loading.
- Show a placeholder while loading:

```jsx
const [loaded, setLoaded] = useState(false);

<View>
  {!loaded && <ActivityIndicator />}
  <Image
    source={{ uri: url }}
    style={{ width: 100, height: 100 }}
    onLoad={() => setLoaded(true)}
  />
</View>
```

---

## Summary

- **Lazy loading** = load only what is needed, when it is needed.
- **Lists:** use `FlatList`, tune `initialNumToRender` and `windowSize`, wrap rows in `React.memo`.
- **Data:** paginate with `onEndReached` (infinite scroll).
- **Screens:** tabs and stacks are lazy by default.
- **Components:** `React.lazy` + `Suspense` for heavy components.
- **Images:** load inside `FlatList`, use a caching library for many images.
