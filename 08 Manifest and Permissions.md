# AndroidManifest.xml and the Permission System in Android

## What is AndroidManifest.xml for?

The **AndroidManifest.xml** file is one of the most important files in every Android application. It is located in the project's main directory (`app/src/main/AndroidManifest.xml`) and serves several key functions:

- **Declares application components** – such as activities (`<activity>`), services (`<service>`), receivers (`<receiver>`), and content providers (`<provider>`).
- **Specifies permissions** – which system resources the app wants to access (e.g., Internet, camera, location).
- **Defines intent filters** – allowing components to handle specific actions or data.
- **Sets basic app information** – such as name, icon, version, package.
- **Declares libraries and special features** – e.g., camera, Bluetooth, NFC support.

> **Note:** Minimum and target system versions (`minSdkVersion`, `targetSdkVersion`) are now declared in the `build.gradle` file, not in AndroidManifest.xml.

**Example fragment of AndroidManifest.xml:**
```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.myapp">

    <application
        android:label="My Application"
        android:icon="@mipmap/ic_launcher">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

    <!-- Permissions -->
    <uses-permission android:name="android.permission.INTERNET" />
</manifest>
```

---

## Permission System in Android

To use certain system features or user data (e.g., camera, location, contacts), the app must declare the appropriate **permissions** in AndroidManifest.xml.

### Declaring Permissions

Add the relevant `<uses-permission>` entry to the manifest file, e.g.:
```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

### Normal and Dangerous Permissions

- **Normal permissions** – are automatically granted during installation (e.g., Internet access).
- **Dangerous permissions** – require additional user consent while the app is running (e.g., location, contacts, SMS).

### Requesting Permission at Runtime

Since Android 6.0 (API 23), the user must grant dangerous permissions while using the app.

**Modern approach (Kotlin, Jetpack Activity Result API):**

It's best to use the modern `ActivityResultContracts` API, which is simpler and safer than the old `onRequestPermissionsResult`.

**Example:**
```kotlin
// In a composable or activity:
val launcher = rememberLauncherForActivityResult(
    contract = ActivityResultContracts.RequestPermission()
) { isGranted: Boolean ->
    if (isGranted) {
        // Permission granted
    } else {
        // Permission denied
    }
}

// Request permission, e.g., on button click:
Button(onClick = {
    launcher.launch(Manifest.permission.CAMERA)
}) {
    Text("Request camera access")
}
```

**For multiple permissions:**
```kotlin
val launcher = rememberLauncherForActivityResult(
    contract = ActivityResultContracts.RequestMultiplePermissions()
) { permissions ->
    val cameraGranted = permissions[Manifest.permission.CAMERA] ?: false
    val locationGranted = permissions[Manifest.permission.ACCESS_FINE_LOCATION] ?: false
    // Handle responses
}

launcher.launch(arrayOf(
    Manifest.permission.CAMERA,
    Manifest.permission.ACCESS_FINE_LOCATION
))
```

---

## Summary

- **AndroidManifest.xml** is the central configuration file for the app – declare components, permissions, and other key info there.
- **Permissions** protect user privacy – the app must request access to sensitive data and features.
- Since Android 6.0, some permissions require user consent at runtime.

## 📱Showcase application:
- [Manifest showcase](https://github.com/MarcinRod/ManifestShowcaseSimple)
  
## More information:
- [Permissions overview – Android Developers](https://developer.android.com/guide/topics/permissions/overview)
- [Request app permissions – Android Developers](https://developer.android.com/training/permissions/requesting)
---
### 🧭 **Next topic:** [App Architecture](https://github.com/MarcinRod/AndroidLecture2025EN/blob/main/09%20App%20Architecture.md)
