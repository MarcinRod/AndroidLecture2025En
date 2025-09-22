# Firebase Platform

**Firebase** is a Google platform offering a set of tools and services for quickly building, developing, and managing mobile and web applications. With Firebase, you can easily add features like cloud databases, user authentication, push notifications, analytics, hosting, and more to your app.

---

## Key Firebase Services

- **Firebase Authentication** – easy user authentication (email, Google, Facebook, anonymous, and more).
- **Cloud Firestore** – modern, scalable NoSQL cloud database with real-time data synchronization.
- **Realtime Database** – legacy real-time NoSQL database.
- **Firebase Cloud Messaging (FCM)** – push notifications for mobile and web apps.
- **Firebase Analytics** – advanced user and event analytics.
- **Firebase Storage** – cloud file storage (e.g., photos, documents).
- **Firebase Hosting** – hosting for static websites.
- **Remote Config** – remote configuration of app parameters without store updates.
- **Crashlytics** – real-time crash and error reporting.
- **Test Lab** – cloud-based app testing on multiple devices.

---

## How to Start Using Firebase in Android?

1. **Create an account and project in the [Firebase Console](https://console.firebase.google.com/).**
2. **Add your Android app to the Firebase project** (download the `google-services.json` file).
3. **Add dependencies to your `build.gradle` file:**
   ```groovy
   implementation platform('com.google.firebase:firebase-bom:32.7.0')
   implementation 'com.google.firebase:firebase-analytics-ktx'
   // Add other dependencies as needed, e.g.:
   // implementation 'com.google.firebase:firebase-auth-ktx'
   // implementation 'com.google.firebase:firebase-firestore-ktx'
   ```
4. **Add the plugin to your project-level `build.gradle`:**
   ```groovy
   classpath 'com.google.gms:google-services:4.4.1'
   ```
5. **Apply the plugin at the end of your module/app `build.gradle`:**
   ```groovy
   apply plugin: 'com.google.gms.google-services'
   ```

---

## Firebase Authentication – User Authentication

**Firebase Authentication** enables quick and secure login for your app. It supports many authentication methods, including email/password, Google, Facebook, anonymous accounts, and more.

### How to Add Firebase Authentication to Your Project?

1. Add the dependency to your `build.gradle`:
   ```groovy
   implementation 'com.google.firebase:firebase-auth-ktx'
   ```

2. Configure your chosen login methods in the [Firebase Console](https://console.firebase.google.com/) (e.g., email, Google).

---

### Example: Registering and Logging in a User with Email and Password

```kotlin
import com.google.firebase.auth.FirebaseAuth

val auth = FirebaseAuth.getInstance()

// Register a new user
auth.createUserWithEmailAndPassword(email, password)
    .addOnCompleteListener { task ->
        if (task.isSuccessful) {
            // Registration successful
            val user = auth.currentUser
        } else {
            // Handle error
        }
    }

// Log in a user
auth.signInWithEmailAndPassword(email, password)
    .addOnCompleteListener { task ->
        if (task.isSuccessful) {
            // Login successful
            val user = auth.currentUser
        } else {
            // Handle error
        }
    }
```
#### FirebaseAuth Object

`FirebaseAuth` is the main object for handling user authentication in Firebase. It allows registration, login, logout, password reset, and managing the current user. You get an instance via `FirebaseAuth.getInstance()`.  
With it, you can easily check if a user is logged in (`auth.currentUser`), get their data, or perform account-related operations.

Example of checking if a user is logged in:
```kotlin
val auth = FirebaseAuth.getInstance()
val user = auth.currentUser
if (user != null) {
    // User is logged in
} else {
    // No user logged in
}
```
---

### Key Features of Firebase Authentication

- Support for multiple login methods (email, Google, Facebook, Apple, anonymous, and more).
- Simple integration with your app.
- Automatic user session management.
- Password reset, email verification, user data changes.

**More information:**  
- [Firebase Authentication – documentation](https://firebase.google.com/docs/auth)

---

## Firebase Realtime Database – Real-Time Database

**Firebase Realtime Database** is a cloud NoSQL database that enables storing and synchronizing data between users in real time. Any change in the database is immediately visible to all connected clients.

### How to Add Firebase Realtime Database to Your Project?

1. Add the dependency to your `build.gradle`:
   ```groovy
   implementation 'com.google.firebase:firebase-database-ktx'
   ```

2. Configure Realtime Database in the [Firebase Console](https://console.firebase.google.com/) (create the database, set access rules).

---
### Database Structure in Firebase Realtime Database

Firebase Realtime Database stores data as a **JSON tree**. The entire database is a single large JSON object, where you can create any nested branches (keys and values). There are no tables or relations like in classic SQL databases—everything is based on key-value structure.

**Example database structure:**
```json
{
  "products": {
    "id1": {
      "name": "Coffee",
      "price": 12.99
    },
    "id2": {
      "name": "Tea",
      "price": 8.50
    }
  },
  "users": {
    "uid1": {
      "email": "jan@wp.pl",
      "firstName": "Jan"
    }
  }
}
```

**Key structure features:**
- Each node (branch) can contain further nested data.
- Keys can be auto-generated (`push().key`) or set manually.
- You can reference any place in the tree using a path, e.g., `"products/id1/name"`.
- Data is fetched and synchronized at the selected node level—you can fetch an entire branch or just a fragment.

**Design tips:**
- Avoid overly deep data nesting—prefer a flat structure for easier data fetching and updating.
- If you need relationships (e.g., a product belongs to a user), store identifiers (keys) instead of nesting whole objects.

#### FirebaseDatabase Object

`FirebaseDatabase` is the main object for communicating with Firebase Realtime Database in Android apps. It lets you access the database, create references to selected branches (nodes), and perform operations like write, read, update, and delete data.

Get an instance via:
```kotlin
val database = FirebaseDatabase.getInstance()
```

Key features:
- Access to the entire database or selected fragments (via references).
- Real-time data synchronization.
- Offline support—data is cached locally and synchronized when connection is restored.
- Security and authorization rules at the database level.

**Example usage:**
```kotlin
val database = FirebaseDatabase.getInstance()
val ref = database.getReference("products") // reference to "products" branch
```

With `FirebaseDatabase`, you can easily manage cloud data and synchronize it between multiple users in real time.

### What is a Reference in Firebase Database Structure?

A **reference** in Firebase Realtime Database is an object pointing to a specific place (node) in the database tree. With references, you can read, write, update, or delete data at the selected location.

Create a reference using `getReference()` on the database instance, providing the path to the desired node:

```kotlin
val database = FirebaseDatabase.getInstance()
val ref = database.getReference("products") // reference to "products" branch
```

You can create references to any tree level, e.g.:

```kotlin
val productRef = database.getReference("products/id1") // reference to a specific product
val userRef = database.getReference("users/uid1") // reference to a specific user
```

**What is a reference for?**
- Allows operations on the selected database fragment (e.g., write, read, listen for changes).
- Makes it easy to navigate the JSON tree structure.
- Lets you work only with the data you need, without fetching the entire database.

**Example usage of reference:**
```kotlin
// Write a new product
val ref = database.getReference("products")
val productId = ref.push().key
if (productId != null) {
    ref.child(productId).setValue(product)
}

// Read data from a specific product
val productRef = database.getReference("products/$productId")
productRef.addListenerForSingleValueEvent(object : ValueEventListener {
    override fun onDataChange(snapshot: DataSnapshot) {
        val name = snapshot.child("name").getValue(String::class.java)
        // handle data
    }
    override fun onCancelled(error: DatabaseError) {}
})
```

**Summary:**  
A reference is a "pointer" to a selected branch in Firebase, letting you conveniently manage data in the JSON tree structure.

**More about data structure:**  
- [Designing the database structure in Realtime Database](https://firebase.google.com/docs/database/web/structure-data)

---
### Example: Writing and Reading Data

Below is sample code showing how to write a new product to the database and how to read data from Firebase Realtime Database.

**Write a new product:**
```kotlin
import com.google.firebase.database.FirebaseDatabase

val database = FirebaseDatabase.getInstance()
val ref = database.getReference("products")

// Create a map with product data
val product = mapOf(
    "name" to "Coffee",
    "price" to 12.99
)

// Generate a unique key for the new product
val productId = ref.push().key
if (productId != null) {
    ref.child(productId).setValue(product)
}
```
#### Requirements for the object to be saved

To save an object using `setValue()`, the object should:
- be a simple type (e.g., String, Int, Double, Boolean, Map, List) **or**
- be a data class with public properties and a default (no-argument) constructor.

Example data class:
```kotlin
data class Product(
    val name: String? = null,
    val price: Double? = null
)
```
**Important:**  
If you save a custom object (e.g., `Product`), all its properties must be public and have default values (e.g., `= null`) so Firebase can deserialize the data correctly when reading.

**Reading data (listening for changes in the "products" branch):**
```kotlin
import com.google.firebase.database.DataSnapshot
import com.google.firebase.database.DatabaseError
import com.google.firebase.database.ValueEventListener

ref.addValueEventListener(object : ValueEventListener {
    override fun onDataChange(snapshot: DataSnapshot) {
        for (child in snapshot.children) {
            val name = child.child("name").getValue(String::class.java)
            val price = child.child("price").getValue(Double::class.java)
            // handle data, e.g., add to product list
        }
    }
    override fun onCancelled(error: DatabaseError) {
        // handle error
    }
})
```

**Explanation:**
- Use `setValue()` on a reference to write data at the selected location in the database.
- Use change listeners (`addValueEventListener`) to read data, so the app automatically receives real-time updates.
- You can read an entire branch (e.g., all products) or a single item (e.g., one product by its ID).

**Single data fetch from the database**

If you want to fetch data only once (without listening for changes), use `addListenerForSingleValueEvent`:

```kotlin
val productRef = database.getReference("products/$productId")
productRef.addListenerForSingleValueEvent(object : ValueEventListener {
    override fun onDataChange(snapshot: DataSnapshot) {
        val name = snapshot.child("name").getValue(String::class.java)
        val price = snapshot.child("price").getValue(Double::class.java)
        // handle single product
    }
    override fun onCancelled(error: DatabaseError) {
        // handle error
    }
})
```
**Single data fetch using the get() method**

Firebase Realtime Database allows you to fetch data once without listening for changes, using the `get()` method. This is a convenient alternative to `addListenerForSingleValueEvent`.

**Example usage:**
```kotlin
val ref = FirebaseDatabase.getInstance().getReference("products/$productId")
ref.get().addOnSuccessListener { snapshot ->
    val product = snapshot.getValue(Product::class.java)
    // handle fetched product
}.addOnFailureListener {
    // handle error
}
```

#### What is DataSnapshot?

`DataSnapshot` is an object representing data fetched from a selected place in the database (reference). It allows:
- reading the value of a specific field (`snapshot.child("name").getValue(String::class.java)`),
- iterating over children (e.g., all products in a branch),
- checking if data exists (`snapshot.exists()`).

#### How does getValue work?

The `getValue()` method converts data from the database to the specified type in Kotlin/Java.  
Example:
```kotlin
val name = snapshot.child("name").getValue(String::class.java)
val price = snapshot.child("price").getValue(Double::class.java)
```
You can also fetch the entire object if you have the appropriate data class:
```kotlin
data class Product(val name: String? = null, val price: Double? = null)
val product = snapshot.getValue(Product::class.java)
```
---

### Key Features of Realtime Database

- Real-time data synchronization between all clients.
- Data structure based on JSON tree (NoSQL).
- Offline support—data is cached locally and synchronized when connection is restored.
- Security and authorization rules.

**More information:**  
- [Firebase Realtime Database – documentation](https://firebase.google.com/docs/database)

---

## Advantages of Using Firebase

- Fast integration and ready-made backend solutions.
- Secure user authentication.
- Scalability and reliability of Google services.

---

**More information:**  
- [Official Firebase documentation](https://firebase.google.com/docs/android/setup)
- [Examples and guides](https://firebase.google.com/docs)

---

### 🧭 **Back to main page:** [Start](https://github.com/MarcinRod/AndroidLecture2025EN/tree/main)
