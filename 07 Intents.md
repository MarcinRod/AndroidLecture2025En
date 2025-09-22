# Intent System in Android Applications

**Intents** are one of the fundamental communication mechanisms in Android. They allow you to:

- Launch new activities (screens) or services
- Pass data between application components
- Invoke system functions (e.g., open a browser, send an email)
- Communicate between different applications

---

## Types of Intents

- **Explicit Intent** – specifies exactly which component (e.g., activity) should be launched. Use it when you know the target class name.
  ```kotlin
  val intent = Intent(context, DetailsActivity::class.java)
  context.startActivity(intent)
  ```

- **Implicit Intent** – describes a general action (e.g., "take a photo", "send an email"), and Android chooses the appropriate app or component to handle the action.
  ```kotlin
  val intent = Intent(Intent.ACTION_VIEW, Uri.parse("https://android.com"))
  context.startActivity(intent)
  ```

---
## Passing Data with Intents

Intents not only launch other components but also allow you to pass data to them. The most common way is using **extras**—additional information attached to the intent.

### Passing Data to an Activity

**Adding data to an intent:**
```kotlin
val intent = Intent(context, DetailsActivity::class.java)
intent.putExtra("id", 123)
intent.putExtra("name", "Product X")
context.startActivity(intent)
```

**Receiving data in the activity:**
```kotlin
class DetailsActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        val id = intent.getIntExtra("id", 0)
        val name = intent.getStringExtra("name")
        // ...use the data
    }
}
```

### Passing Data to Other Applications

You can pass data to other apps, e.g., by sending text or an image:

```kotlin
val intent = Intent(Intent.ACTION_SEND)
intent.type = "text/plain"
intent.putExtra(Intent.EXTRA_TEXT, "This is a message!")
context.startActivity(Intent.createChooser(intent, "Choose an app"))
```

### Passing Complex Objects

To pass an object, it must implement the `Serializable` or `Parcelable` interface:

```kotlin
val intent = Intent(context, DetailsActivity::class.java)
intent.putExtra("product", product) // where product : Parcelable
context.startActivity(intent)
```
In the activity, receive it as:
```kotlin
val product = intent.getParcelableExtra<Product>("product")
```

### **Summary:**
- Use `putExtra` and corresponding getter methods (`getIntExtra`, `getStringExtra`, etc.) for simple data.
- Use `Parcelable` or `Serializable` for objects.
- Data passing via intents works both within the app and between different apps.

---

### Intent Filter System

**Intent Filter** is a mechanism that lets an app declare which actions, data, or categories it can handle. Intent filters are defined in the `AndroidManifest.xml` file and allow Android to choose the right app or component to handle a given intent.

**How does it work?**
- When you send an implicit intent, the system searches all installed apps and their intent filters.
- If it finds a component (e.g., activity) that matches the action, data type, or category, it shows the user a chooser or launches the appropriate app.

**Example intent filter in AndroidManifest.xml:**
```xml
<activity android:name=".DetailsActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <data android:scheme="https" android:host="android.com" />
    </intent-filter>
</activity>
```

**What can you filter?**
- **Actions** (e.g., `android.intent.action.SEND`)
- **Categories** (e.g., `android.intent.category.DEFAULT`)
- **Data types** (e.g., `image/*`, `text/plain`)
- **URI schemes and hosts** (e.g., `https://android.com`)

---

## Example Usage of Intents

**Launching a new activity with data:**
```kotlin
val intent = Intent(context, DetailsActivity::class.java)
intent.putExtra("id", 123)
context.startActivity(intent)
```

**Invoking a system action (e.g., opening a webpage):**
```kotlin
val intent = Intent(Intent.ACTION_VIEW, Uri.parse("https://android.com"))
context.startActivity(intent)
```

---

## Intents vs. Navigation Compose

In traditional Android apps, navigation between screens (activities) is done using intents. In Jetpack Compose, navigation **within a single activity** is handled by Navigation Compose and `NavController`.

- **Navigation Compose** manages transitions between composables (screens) within a single activity.
- **Intents** are still used for:
  - Launching other activities (e.g., moving to a login screen in another app)
  - Invoking system functions
  - Communication between apps

**Example: launching another activity from a composable:**
```kotlin
val context = LocalContext.current
Button(onClick = {
    val intent = Intent(context, DetailsActivity::class.java)
    intent.putExtra("id", 123)
    context.startActivity(intent)
}) {
    Text("Open details (new activity)")
}
```

**Example: navigation in Compose (without intents):**
```kotlin
Button(onClick = { navController.navigate("details/123") }) {
    Text("Go to details (in Compose)")
}
```

---

## Summary

- **Intents** are used for communication between activities, services, and apps.
- **Navigation Compose** handles navigation between screens within a single activity.
- In modern Compose apps, both mechanisms often coexist: Navigation Compose for internal navigation, intents for system and inter-app communication.

---
### 🧭 **Next topic:** [Manifest and Permissions](https://github.com/MarcinRod/AndroidLecture2025EN/blob/main/08%20Manifest%20and%20Permissions.md)
