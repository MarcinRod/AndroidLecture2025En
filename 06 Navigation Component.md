# Navigation Component in Jetpack Compose

## Introduction
Navigation Compose is the recommended way to handle navigation between screens in Jetpack Compose applications. It provides a declarative API for managing navigation graphs, back stack, and passing arguments between destinations.

---
## Navigation Basics

- **Navigation Graph**: Defines all possible destinations (screens) and the relationships between them.
- **NavHost**: The container that hosts navigation destinations.
- **NavController**: The object responsible for navigation actions and managing the back stack.
- **Composable Destinations**: Each screen is defined as a composable function.

### Example: Basic Navigation Setup
```kotlin
val navController = rememberNavController()
NavHost(navController, startDestination = "screenA") {
    composable("screenA") { ScreenA(navController) }
    composable("screenB") { ScreenB(navController) }
}
```

---
## Navigating Between Screens – NavController Methods

The `NavController` object is used for navigation between screens in Jetpack Compose. Here are the most important methods:

### 1. Navigate to Another Screen
```kotlin
navController.navigate("screenB")
```

### 2. Go Back to Previous Screen
```kotlin
navController.popBackStack()
```

### 3. Go Back to a Specific Destination
```kotlin
navController.popBackStack("screenA", inclusive = false)
```
- If `inclusive = true`, the destination "screenA" will also be removed from the stack.

### 4. Avoid Duplicating Screens (singleTop)
```kotlin
navController.navigate("screenA") {
    launchSingleTop = true
}
```

### 5. Navigate and Clear Back Stack (e.g., after login)
```kotlin
navController.navigate("startScreen") {
    popUpTo("loginScreen") { inclusive = true }
}
```

---
**Summary:**
- `navigate(route)` – go to a screen
- `popBackStack()` – go back to previous screen
- `popBackStack(route, inclusive)` – go back to a specific destination
- Navigation options (`launchSingleTop`, `popUpTo`) allow you to control the navigation stack

These methods let you fully manage user flow in a Compose app.

---
### Best Practices for Defining Navigation Destinations

To keep navigation code readable, safe, and maintainable, avoid hardcoded strings for route definitions. Instead, use:

#### 1. Enum Class
Define all destinations as enum values:
```kotlin
enum class Destination(val route: String) {
    ScreenA("screenA"),
    ScreenB("screenB"),
    Details("details/{id}")
}

// Usage:
NavHost(navController, startDestination = Destination.ScreenA.route) {
    composable(Destination.ScreenA.route) { ScreenA(navController) }
    composable(Destination.ScreenB.route) { ScreenB(navController) }
    composable(Destination.Details.route) { /* ... */ }
}
```

#### 2. Sealed Class (More Flexible)
Sealed classes allow passing arguments and generating routes dynamically:
```kotlin
sealed class Destination(val route: String) {
    object ScreenA : Destination("screenA")
    object ScreenB : Destination("screenB")
    data class Details(val id: String) : Destination("details/$id") {
        companion object {
            const val routePattern = "details/{id}"
        }
    }
}

// Usage:
NavHost(navController, startDestination = Destination.ScreenA.route) {
    composable(Destination.ScreenA.route) { ScreenA(navController) }
    composable(Destination.ScreenB.route) { ScreenB(navController) }
    composable(Destination.Details.routePattern) { backStackEntry ->
        val id = backStackEntry.arguments?.getString("id") ?: ""
        DetailsScreen(id)
    }
}

// Navigate with argument:
navController.navigate(Destination.Details("123").route)
```

#### Advantages
- **Type safety** – less risk of typos and route errors.
- **Easier expansion** – adding a new destination is just a new object in enum/sealed class.
- **Better readability** – all routes are collected in one place.

This approach is especially recommended for larger Compose projects.

---
## Passing Arguments

Navigation Compose allows you to pass arguments to destinations and read them in a type-safe way using `navArgument`. You can enforce argument types or set default values.

You can pass data between screens in two ways:

### 1. Path Arguments
Arguments in the path are part of the route, e.g., `"details/{id}"`. They are required and must always be provided when navigating.

**Example:**
```kotlin
NavHost(navController, startDestination = "details/{id}") {
    composable(
        route = "details/{id}",
        arguments = listOf(navArgument("id") { type = NavType.IntType })
    ) { backStackEntry ->
        val id = backStackEntry.arguments?.getInt("id") ?: 0
        DetailsScreen(id)
    }
}

// Navigate with argument:
navController.navigate("details/123")
```

### 2. Query Arguments (Optional, after `?`)
Query arguments are passed after the question mark in the route, e.g., `"screenB?name={name}"`. They can be optional and have default values.

**Example:**
```kotlin
NavHost(navController, startDestination = "screenA") {
    composable(
        route = "screenB?name={name}",
        arguments = listOf(navArgument("name") {
            type = NavType.StringType
            defaultValue = "Guest"
            nullable = true
        })
    ) { backStackEntry ->
        val name = backStackEntry.arguments?.getString("name")
        ScreenB(name)
    }
}

// Navigate without argument:
navController.navigate("screenB")
// Navigate with argument:
navController.navigate("screenB?name=Anna")
```

### navArguments in Enum/Sealed Class Routes

Best practice is to separate the **route pattern** (e.g., `"details/{id}"` or `"screenB?name={name}"`) from the **actual route** (e.g., `"details/123"` or `"screenB?name=Anna"`). This lets you use type-safe argument passing (`navArgument`) and maintain type safety.

#### Example with Sealed Class – Path Argument
```kotlin
sealed class Destination(val route: String) {
    object ScreenA : Destination("screenA")
    object ScreenB : Destination("screenB")
    object Details : Destination("details/{id}") {
        fun createRoute(id: String) = "details/$id"
        const val ARG_ID = "id"
    }
}
```

**NavHost definition with navArgument:**
```kotlin
NavHost(navController, startDestination = Destination.ScreenA.route) {
    composable(Destination.ScreenA.route) { ScreenA(navController) }
    composable(Destination.ScreenB.route) { ScreenB(navController) }
    composable(
        route = Destination.Details.route,
        arguments = listOf(navArgument(Destination.Details.ARG_ID) { type = NavType.StringType })
    ) { backStackEntry ->
        val id = backStackEntry.arguments?.getString(Destination.Details.ARG_ID) ?: ""
        DetailsScreen(id)
    }
}
```

**Navigate with argument:**
```kotlin
navController.navigate(Destination.Details.createRoute("123"))
```

---
#### Example with Sealed Class – Query Argument (Optional)
```kotlin
sealed class Destination(val route: String) {
    object ScreenA : Destination("screenA")
    object ScreenB : Destination("screenB?name={name}") {
        fun createRoute(name: String?) =
            if (name != null) "screenB?name=$name" else "screenB"
        const val ARG_NAME = "name"
    }
}
```

**NavHost definition with navArgument:**
```kotlin
NavHost(navController, startDestination = Destination.ScreenA.route) {
    composable(Destination.ScreenA.route) { ScreenA(navController) }
    composable(
        route = Destination.ScreenB.route,
        arguments = listOf(navArgument(Destination.ScreenB.ARG_NAME) {
            type = NavType.StringType
            defaultValue = "Guest"
            nullable = true
        })
    ) { backStackEntry ->
        val name = backStackEntry.arguments?.getString(Destination.ScreenB.ARG_NAME)
        ScreenB(name)
    }
}
```

**Navigate with or without argument:**
```kotlin
navController.navigate(Destination.ScreenB.createRoute("Anna")) // screenB?name=Anna
navController.navigate(Destination.ScreenB.createRoute(null))   // screenB
```

This approach lets you conveniently and safely pass both required (path) and optional (query) arguments in routes defined by enum or sealed class.

---
## Nested Navigation Graphs

In larger Jetpack Compose apps, **nested navigation graphs** are often used to better organize routes and manage complex screen flows. This allows you to split the app into logical modules (e.g., login, main screen, settings), each with its own navigation subgraph.

### How to Define a Nested Graph?
Inside the main `NavHost`, you can add additional graphs using the `navigation` function:
```kotlin
NavHost(navController, startDestination = "auth") {
    // Main login subgraph
    navigation(startDestination = "login", route = "auth") {
        composable("login") { LoginScreen(navController) }
        composable("register") { RegisterScreen(navController) }
    }
    // Main app subgraph
    navigation(startDestination = "home", route = "main") {
        composable("home") { HomeScreen(navController) }
        composable("settings") { SettingsScreen(navController) }
    }
}
```

### Navigating Between Subgraphs
To navigate to a screen in another subgraph, use the full route (e.g., `"main/home"` or `"auth/login"`):
```kotlin
navController.navigate("main/home")
```

### Advantages of Nested Graphs
- **Better code organization** – each module has its own clear subgraph.
- **Easier flow management** – e.g., you can easily clear the login stack after logging in and move to the main graph.
- **Reusability** – e.g., the same settings subgraph can be used in different parts of the app.

**More about nested graphs:**
[Official Documentation – Nested Navigation Graphs](https://developer.android.com/jetpack/compose/navigation#nested-nav)

---
## Advantages of Navigation Compose

- **Declarative management of routes and screens**  
  All routes and screens are defined in one place, making code easier to read and maintain. The navigation structure is clear and easy to expand.

- **Easy argument passing**  
  You can pass arguments both in the path and as query parameters, with full type and default value support via `navArgument`.

- **Type safety and readability**  
  Using enum and sealed class helps avoid typos and route errors, and makes app development easier to manage.

- **Back stack management**  
  Navigation Compose automatically manages screen history (back stack), allowing intuitive use of the back button and user flow control.

- **Integration with animations and navigation elements**  
  Easily combine navigation with transition animations, bottom navigation bar, tabs, or drawer.

- **Support for nested graphs**  
  You can organize your app into logical modules and manage complex screen flows with nested navigation graphs.

- **Easy integration with app architecture**  
  Navigation Compose works well with ViewModels, State Hoisting, and other Compose architectural patterns.

---
Navigation Compose is a modern and recommended way to handle navigation in Jetpack Compose apps, greatly simplifying screen flow management and improving code quality.

---
## Documentation

- [Official Navigation Compose Documentation](https://developer.android.com/jetpack/compose/navigation)
- [GitHub Examples](https://github.com/android/compose-samples/tree/main/NavigationAdvancedSample)

Navigation Compose is a modern and recommended way to handle navigation in Jetpack Compose apps.

---
### 🧭 **Next topic:** [Intents](https://github.com/MarcinRod/AndroidLecture2025EN/blob/main/07%20Intents.md)
