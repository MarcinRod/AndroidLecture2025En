# 🧩 Composable Functions in Jetpack Compose

## 🔹 What Are Composable Functions?

**Composable functions** are the basic building blocks of the user interface in Jetpack Compose. These are Kotlin functions marked with the `@Composable` annotation that describe how a piece of UI should look.

---

## 🏷️ Naming Composable Functions

- Composable functions should use **PascalCase** (each word capitalized, no underscores), e.g., `UserCard`, `LoginScreen`, `Greeting`.
- The function name should clearly indicate **what the function displays or does**—avoid generic names like `MyComposable` or `TestFunction`.
- If a composable represents a whole screen, end its name with `Screen`, e.g., `ProfileScreen`, `SettingsScreen`.
- For smaller, reusable elements, use names describing their role, e.g., `UserAvatar`, `ProductItem`, `ErrorMessage`.
- If the function is **private to the file** (should not be used outside), you can prefix it with `_`, e.g., `_UserAvatar`. This Compose convention signals the function is "internal".
- Avoid abbreviations and unclear names—Compose code should be self-describing.
- Composable functions should not use imperative verbs (e.g., `ShowUser`), but rather nouns or noun-adjective combinations (`UserCard`, `ErrorDialog`).

**Good naming examples:**
- `LoginScreen`
- `ProfileHeader`
- `ProductListItem`
- `_UserAvatar` (private function)
- `ErrorSnackbar`

**Bad naming examples:**
- `doLoginComposable`
- `myComposable1`
- `ShowProfile`
- `testFun`

Good naming makes code easier to read, test, and reuse in larger Compose projects.

---

## 🧱 The Role of `Modifier`

- **Modifier** is a special object in Compose that lets you modify the appearance, size, position, and behavior of UI elements.
- Modifier is passed as a parameter to most composables and allows you to "chain" multiple effects.
- Any composable accepting a `Modifier` should have it as the first default parameter, e.g.:
  ```kotlin
  @Composable
  fun MyButton(
      onClick: () -> Unit,
      modifier: Modifier = Modifier
  ) {
      Button(onClick = onClick, modifier = modifier) {
          Text("Click me")
      }
  }
  ```
- This makes it easy to combine modifiers and pass them from outside, e.g.:
  ```kotlin
  MyButton(
      onClick = { /* ... */ },
      modifier = Modifier
          .padding(16.dp)
          .fillMaxWidth()
  )
  ```

### ✨ Most Common Modifier Functions

- `padding(...)` – adds inner spacing around the element.
- `fillMaxWidth()`, `fillMaxHeight()`, `fillMaxSize()` – stretches the element to the parent's max width/height/size.
- `size(...)`, `width(...)`, `height(...)` – sets the element's size.
- `background(color)` – sets the element's background.
- `clickable { ... }` – handles clicks on any element.
- `weight(...)` – distributes space in Row/Column proportionally.
- `offset(...)` – moves the element relative to its position.
- `align(...)` – sets alignment in a container (e.g., Box).
- `wrapContentWidth()`, `wrapContentHeight()` – fits size to content.

**Example of combining modifiers:**
```kotlin
Text(
    text = "Example",
    modifier = Modifier
        .padding(8.dp)
        .background(Color.LightGray)
        .fillMaxWidth()
        .clickable { /* handle click */ }
)
```
> **Tip:**  
> The order of calls matters—the first modifier in the chain is applied first, then the next.

Modifiers let you build flexible, responsive, and interactive UIs in Compose without inheriting views or using complex layouts.

---

## 🔄 State Hoisting

- **State hoisting** is a pattern where state and its change function are passed from a parent composable to a child.
- This makes composables more reusable, easier to test, and easier to reuse, because they don't manage their own state internally but receive it from outside.
- State hoisting gives better control over app logic and makes it easier to manage state lifecycle (e.g., resetting, saving, syncing with ViewModel).

### What Does It Look Like in Practice?

Instead of:

```kotlin
@Composable
fun Counter() {
    var count by remember { mutableStateOf(0) }
    Button(onClick = { count++ }) {
        Text("Clicked: $count")
    }
}
```

Use state hoisting:

```kotlin
@Composable
fun Counter(count: Int, onIncrement: () -> Unit) {
    Button(onClick = onIncrement) {
        Text("Clicked: $count")
    }
}

@Composable
fun CounterScreen() {
    var count by remember { mutableStateOf(0) }
    Counter(count = count, onIncrement = { count++ })
}
```

- The `Counter` function no longer manages its own state—it receives it as a parameter and takes a function to change it.
- The parent function (`CounterScreen`) manages the state and passes it to the child composable.

### Advantages of State Hoisting

- **Reusability:** The same composable can be used in different places with different state.
- **Testability:** Easier to test composables that don't have their own state.
- **Predictability:** State is managed in one place, making debugging and maintenance easier.
- **Integration with ViewModel:** Hoisted state is easy to connect to a ViewModel or other data source.

### When NOT to Hoist State?

- If the state is **purely local** and doesn't affect other UI elements, you can keep it inside the composable (e.g., menu expansion, local animation effect).

---

> **More on state hoisting:**  
> [State hoisting – official guide](https://developer.android.com/jetpack/compose/state#hoisting)
---
## 📦 Basic Containers and Commonly Used Elements

In Compose, containers organize and arrange UI elements relative to each other. Here are the most important ones with commonly used parameters:
- **Column** – arranges elements vertically.
- **Row** – arranges elements horizontally.
- **Box** – overlays elements (positioning).
- **LazyColumn** – vertically scrolling list (like RecyclerView).
- **LazyRow** – horizontally scrolling list.
- **Card** – container with rounded corners and shadow.
- **Surface** – basic container with background and elevation.
- **Spacer** – element for spacing.
- **Scaffold** – container for building typical app layouts (e.g., with top bar, bottom bar, FAB).
  
### Examples
- **Column**
  - Arranges elements vertically, one below the other.
  - Common parameters:
    - `modifier` – modifier for the whole column (size, padding).
    - `verticalArrangement` – vertical arrangement of elements (e.g., `Arrangement.SpaceBetween`, `Arrangement.Center`).
    - `horizontalAlignment` – horizontal alignment of elements (e.g., `Alignment.CenterHorizontally`).
  - Example:
    ```kotlin
    Column(
        modifier = Modifier.fillMaxWidth(),
        verticalArrangement = Arrangement.Center,
        horizontalAlignment = Alignment.CenterHorizontally
    ) {
        Text("First")
        Text("Second")
    }
    ```

- **Row**
  - Arranges elements horizontally, side by side.
  - Common parameters:
    - `modifier`
    - `horizontalArrangement` – horizontal arrangement of elements.
    - `verticalAlignment` – vertical alignment of elements.
  - Example:
    ```kotlin
    Row(
        modifier = Modifier.fillMaxWidth(),
        horizontalArrangement = Arrangement.SpaceBetween,
        verticalAlignment = Alignment.CenterVertically
    ) {
        Button(onClick = {}) { Text("OK") }
        Button(onClick = {}) { Text("Cancel") }
    }
    ```

- **Box**
  - Overlays elements (positions them relative to each other).
  - Common parameters:
    - `modifier`
    - `contentAlignment` – default alignment for all children (e.g., `Alignment.Center`).
  - Example:
    ```kotlin
    Box(
        modifier = Modifier.size(100.dp),
        contentAlignment = Alignment.BottomEnd
    ) {
        Image(painter, contentDescription = null)
        Icon(Icons.Default.Favorite, contentDescription = null)
    }
    ```

- **LazyColumn**
  - Vertically scrolling list (like RecyclerView).
  - Common parameters:
    - `modifier`
    - `contentPadding` – padding inside the list.
    - `verticalArrangement`
    - `horizontalAlignment`
  - Example:
    ```kotlin
    LazyColumn(
        modifier = Modifier.fillMaxSize(),
        contentPadding = PaddingValues(16.dp)
    ) {
        items(100) { index ->
            Text("Item #$index")
        }
    }
    ```

- **LazyRow**
  - Horizontally scrolling list.
  - Common parameters:
    - `modifier`
    - `contentPadding`
    - `horizontalArrangement`
    - `verticalAlignment`
  - Example:
    ```kotlin
    LazyRow(
        contentPadding = PaddingValues(horizontal = 8.dp)
    ) {
        items(10) { index ->
            Card(modifier = Modifier.size(80.dp)) {
                Text("Card $index")
            }
        }
    }
    ```

- **Card**
  - Container with rounded corners and shadow.
  - Common parameters:
    - `modifier`
    - `shape` – card shape (e.g., RoundedCornerShape).
    - `elevation` – card shadow.
    - `backgroundColor`
  - Example:
    ```kotlin
    Card(
        modifier = Modifier.padding(8.dp),
        shape = RoundedCornerShape(16.dp),
        elevation = CardDefaults.cardElevation(8.dp)
    ) {
        Text("This is a card")
    }
    ```

- **Surface**
  - Basic container with background and elevation.
  - Common parameters:
    - `modifier`
    - `color`
    - `shape`
    - `shadowElevation`
  - Example:
    ```kotlin
    Surface(
        color = Color.White,
        shape = RoundedCornerShape(8.dp),
        shadowElevation = 4.dp
    ) {
        Text("Content")
    }
    ```

- **Spacer**
  - Element for spacing between other elements.
  - Common parameters:
    - `modifier` (usually `Modifier.height(...)` or `Modifier.width(...)`)
  - Example:
    ```kotlin
    Spacer(modifier = Modifier.height(16.dp))
    ```

- **Scaffold**
  - `Scaffold` is a container that makes it easy to build standard app layouts following Material Design. 
  - Lets you easily add a top bar (`TopAppBar`), bottom bar (`BottomAppBar`), FAB, navigation drawer, and other elements.

```kotlin
@Composable
fun MainScreen() {
    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text("App Title") }
            )
        },
        floatingActionButton = {
            FloatingActionButton(onClick = { /* action */ }) {
                Icon(Icons.Default.Add, contentDescription = "Add")
            }
        },
        bottomBar = {
            BottomAppBar {
                Text("Bottom bar")
            }
        }
    ) { innerPadding ->
        // Main screen content
        Column(modifier = Modifier.padding(innerPadding)) {
            Text("Welcome to the app!")
        }
    }
}
```

**Common Scaffold parameters:**
- `topBar` – top bar (e.g., `TopAppBar`)
- `bottomBar` – bottom bar (e.g., `BottomAppBar`)
- `floatingActionButton` – FAB
- `drawerContent` – navigation drawer content
- `snackbarHost` – snackbar handling
- `content` – main screen content (receives padding from Scaffold)
---


## ⭐ Commonly Used UI Elements

Jetpack Compose provides many ready-to-use composables for building modern UIs quickly. Here are the most important and frequently used:

- **Text**
  - Displays text.
  - Key parameters:
    - `text` – displayed text.
    - `modifier` – appearance and position modifier.
    - `style` – text style (e.g., `MaterialTheme.typography.bodyLarge`).
    - `color` – text color.
    - `maxLines`, `overflow` – control line count and overflow behavior.
  - Example:
    ```kotlin
    Text(
        text = "Welcome to Compose!",
        style = MaterialTheme.typography.titleLarge,
        color = Color.Blue,
        maxLines = 1,
        overflow = TextOverflow.Ellipsis
    )
    ```

- **Button**
  - Classic button.
  - Key parameters:
    - `onClick` – click handler.
    - `modifier`
    - `enabled` – whether the button is active.
    - `content` – button content (e.g., `Text`, `Icon`).
  - Example:
    ```kotlin
    Button(onClick = { /* action */ }) {
        Text("Confirm")
    }
    ```

- **OutlinedButton**, **IconButton**
  - Button variants: with border or icon only.
  - Example:
    ```kotlin
    OutlinedButton(onClick = { }) { Text("Cancel") }
    IconButton(onClick = { }) { Icon(Icons.Default.Favorite, contentDescription = null) }
    ```

- **TextField**
  - Text input field.
  - Key parameters:
    - `value` – current text value.
    - `onValueChange` – text change handler.
    - `label` – field label.
    - `placeholder` – hint.
    - `singleLine` – whether the field is single-line.
    - `modifier`
  - Example:
    ```kotlin
    var text by remember { mutableStateOf("") }
    TextField(
        value = text,
        onValueChange = { text = it },
        label = { Text("Name") },
        singleLine = true
    )
    ```

- **Checkbox**, **Switch**, **RadioButton**
  - Selection elements.
  - Key parameters:
    - `checked` – checked state.
    - `onCheckedChange` – state change handler.
    - `modifier`
  - Example:
    ```kotlin
    var checked by remember { mutableStateOf(false) }
    Checkbox(checked = checked, onCheckedChange = { checked = it })
    Switch(checked = checked, onCheckedChange = { checked = it })
    RadioButton(selected = checked, onClick = { checked = !checked })
    ```

- **Image**, **Icon**
  - Display images and icons.
  - Key parameters:
    - `painter` (for Image), `imageVector` (for Icon)
    - `contentDescription` – accessibility description.
    - `modifier`
    - `contentScale` – image scaling mode.
  - Example:
    ```kotlin
    Image(
        painter = painterResource(id = R.drawable.avatar),
        contentDescription = "Avatar",
        modifier = Modifier.size(64.dp),
        contentScale = ContentScale.Crop
    )
    Icon(Icons.Default.Home, contentDescription = "Home")
    ```

- **Divider**
  - Divider line, e.g., between list items.
  - Key parameters:
    - `modifier`
    - `color`
    - `thickness`
  - Example:
    ```kotlin
    Divider(color = Color.Gray, thickness = 1.dp)
    ```

---

## 👁️ Previewing Composable Functions

Jetpack Compose lets you quickly preview UI without running the app on an emulator or device.

- Use the `@Preview` annotation above a composable function (or its call).
- The preview function must be parameterless or have default values.

**Example:**
```kotlin
@Preview(showBackground = true, name = "Greeting Preview", widthDp = 320)
@Composable
fun GreetingPreview() {
    Greeting(name = "Compose")
}
```
- The preview appears in the "Design" panel in Android Studio.

### Key `@Preview` Annotation Parameters

- **`name`** – preview name shown in Android Studio (useful when you have several previews).
- **`showBackground`** – whether to show background around the composable (default `false`). Set to `true` to better see shape and margins.
- **`backgroundColor`** – preview background color in ARGB format (e.g., `0xFFFF0000` for red). Works only if `showBackground = true`.
- **`widthDp`** and **`heightDp`** – force preview size in dp (useful for testing responsiveness).
- **`group`** – group several previews together (e.g., different color variants).
- **`uiMode`** – force light/dark mode (`Configuration.UI_MODE_NIGHT_YES` or `NO`).
- **`locale`** – force language/region (e.g., `"pl"` for Polish, `"en"` for English).
- **`fontScale`** – test UI with different font sizes (e.g., `1.5f`).

**Examples:**
```kotlin
@Preview(name = "Dark Mode", uiMode = Configuration.UI_MODE_NIGHT_YES, showBackground = true)
@Composable
fun DarkModePreview() {
    MyScreen()
}

@Preview(locale = "pl", showBackground = true, widthDp = 400)
@Composable
fun PolishPreview() {
    Greeting(name = "User")
}
```
> **Tip:**  
> You can have multiple previews for one function to quickly check different variants (e.g., dark mode, different languages, screen sizes).
> 
Preview in Compose speeds up UI work, lets you test variants, and helps catch visual bugs faster.

---

## 🧠 Best Practices

- Split UI into small, reusable composables.
- Pass state and event handlers via parameters (state hoisting).
- Always accept `Modifier` as the first default parameter.
- Use preview (`@Preview`) for quick appearance testing.

---

## 📚 Documentation

- [Official Jetpack Compose Documentation – Composables](https://developer.android.com/jetpack/compose/composables)
- [Compose Pathway – Compose basics](https://developer.android.com/jetpack/compose/tutorial)
- [Compose – Preview](https://developer.android.com/jetpack/compose/tooling/preview)
- [State hoisting – official guide](https://developer.android.com/jetpack/compose/state#hoisting)

---
### 🧭 **Next topic:** [Navigation Component](https://github.com/MarcinRod/AndroidLecture2025EN/blob/main/06%20Navigation%20Component.md)
