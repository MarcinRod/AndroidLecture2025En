# Background Work in Android Apps (Jetpack Compose and Coroutines)

In modern Android apps, including those built with Jetpack Compose, many operations (e.g., fetching data from the network, saving to a database, long computations) should be performed **in the background** to avoid blocking the main UI thread.

## UI Thread (Main Thread) in Android

All operations related to displaying the user interface (UI) in Android must be performed on the **main thread** (also called the **UI thread**). This means:

- Rendering views, handling clicks, animations, and UI state updates must happen on the same thread.
- If you perform a long operation (e.g., network fetch, database write) on the main thread, the app will become unresponsive, which can lead to an **ANR (Application Not Responding)** error.

---

## Why is Background Work Important?

- **You don't block the user interface** – the app remains responsive.
- **Avoid ANR errors** – the system may kill the app if operations take too long on the main thread.
- **Better user experience** – e.g., loading data from the network doesn't stop animations or scrolling.

---

## Coroutines

In Jetpack Compose, **coroutines** (Kotlin Coroutines) are most commonly used for background work:

- Coroutines make it easy to run asynchronous tasks.
- They are lightweight and integrate well with Compose architecture and ViewModel.

## Coroutine System Elements

### Suspend Functions

- Functions marked with the `suspend` keyword can only be called from a coroutine or another suspend function.
- They allow asynchronous operations to be written sequentially, without blocking the thread.

**Example:**
```kotlin
suspend fun fetchDataFromNetwork(): String {
    // fetch data from network
    return "Data"
}
```

### Scope (Coroutine Scope)

- **Scope** manages coroutines—it determines which part of the app they belong to and when they should be cancelled.
- Most commonly used scopes in Compose:
  - **viewModelScope** – coroutines are tied to the ViewModel and are cancelled when the ViewModel is cleared.
  - **lifecycleScope** – coroutines are tied to the lifecycle of a component (e.g., activity).
  - **rememberCoroutineScope** – scope tied to a composable, manages coroutines in the context of a composable.

### Dispatchers (Thread Selection)

- **Dispatchers** determine which thread the coroutine runs on:
  - `Dispatchers.Main` – main UI thread (default in Compose and ViewModel).
  - `Dispatchers.IO` – for I/O operations (e.g., network, files, database).
  - `Dispatchers.Default` – for heavy computations.
- You can switch between dispatchers using `withContext`.

**Summary:**
- Suspend functions let you write asynchronous code simply.
- Scope manages coroutine lifecycle and cancellation.
- Dispatchers select the right thread for the task.

---

## Launching Coroutines

To perform a background task, you must launch a coroutine in the appropriate scope. Most common ways:

### `launch`

- The most popular function for launching coroutines.
- Used for tasks that don't need to return a result.
- Usually called in `viewModelScope`, `lifecycleScope`, or `rememberCoroutineScope`.

**Example in ViewModel:**
```kotlin
viewModelScope.launch {
    val data = fetchDataFromNetwork()
    // update UI state
}
```

**Example in composable with its own scope:**
```kotlin
val coroutineScope = rememberCoroutineScope()
Button(onClick = {
    coroutineScope.launch {
        // background task, e.g., save to database
    }
}) {
    Text("Save")
}
```

### `async`

- Used when you want to run a task in parallel and get a result as `Deferred`.
- Allows you to run multiple tasks in parallel and get results using `await()`.

**Example:**
```kotlin
viewModelScope.launch {
    val deferred1 = async { fetchData1() }
    val deferred2 = async { fetchData2() }
    val result1 = deferred1.await()
    val result2 = deferred2.await()
    // use both results
}
```

### `LaunchedEffect`

- A special composable function in Compose that launches a coroutine when the composable appears or when the key changes.
- Ideal for initializing data or reacting to parameter changes.

**Example:**
```kotlin
@Composable
fun MyScreen(viewModel: MyViewModel = viewModel()) {
    LaunchedEffect(Unit) {
        viewModel.loadData()
    }
    // UI...
}
```
`LaunchedEffect` launches a coroutine when the composable appears **or** when the key value changes. If you use `Unit` as the key, the effect runs only once when the composable is first displayed. If you use another value (e.g., an ID, state), the effect runs every time that value changes.

**Example:**
```kotlin
@Composable
fun UserScreen(userId: String, viewModel: UserViewModel = viewModel()) {
    LaunchedEffect(userId) {
        viewModel.loadUser(userId)
    }
    // UI...
}
```
In this example:
- If `userId` changes (e.g., the user selects another profile), `LaunchedEffect` will relaunch the coroutine and load the new user's data.
- If you used `Unit` as the key, the effect would run only once, regardless of changes to `userId`.

---

## Background Work and Long-Running Tasks

- For very long tasks (e.g., background sync, notifications), use **WorkManager** or **Foreground Service**.
- Coroutines are ideal for short operations tied to the screen or ViewModel lifecycle.

---

## Recommendations and Best Practices

- **Always launch coroutines in the appropriate scope**  
  This way, coroutines are automatically cancelled when the component (ViewModel, composable, activity) is destroyed. This prevents memory leaks and unnecessary background work.

- **Choose the right dispatcher**  
  Use `Dispatchers.IO` for I/O operations (network, files, database), `Dispatchers.Default` for heavy computations, and `Dispatchers.Main` for UI updates.

- **Don't block the main thread**  
  Always perform long operations in the background (e.g., using `withContext(Dispatchers.IO)`).

- **Use suspend functions for asynchronous operations**  
  This lets you write readable, sequential code without blocking threads.

- **Use LaunchedEffect for initialization and reacting to parameter changes in composables**  
  This ensures tasks run only when needed.
  
## 📱Showcase application:
- [Background tasks](https://github.com/MarcinRod/BackgroundOperationShowcase)
  
## More Information

- [Kotlin Coroutines – Android Developers](https://developer.android.com/kotlin/coroutines)
- [WorkManager – Android Developers](https://developer.android.com/topic/libraries/architecture/workmanager)
- [Background work in Compose – documentation](https://developer.android.com/jetpack/compose/side-effects#launchedeffect)

---

### 🧭 **Next topic:** [Databases - Room](https://github.com/MarcinRod/AndroidLecture2025EN/blob/main/11%20Databases%20-%20Room.md)
