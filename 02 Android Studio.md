# 📁 Android Studio Project Structure and Gradle Scripts

When creating an app in Android Studio, the project consists of many directories and files, each serving a specific role. This chapter covers:

- the most important elements of project structure,
- basics of configuration using Gradle,
- Android Studio as a development environment.

---

### 🛠️ Android Studio – Basics

**Android Studio** is the official IDE (Integrated Development Environment) for Android app development, based on JetBrains' IntelliJ IDEA.

#### 🔑 Key Features of Android Studio:

- Support for **Jetpack Compose**, **ViewModel**, **LiveData**, **Room**, etc.
- Advanced **debugging tools** (Logcat, Debugger, Profiler).
- **Emulator** for testing apps without a physical device.
- **Compose Preview** – preview UI components without running the app.
- Automatic **library and SDK updates**.
- Integration with **Firebase**, **Git**, **Gradle**.
- **Plugins** for many languages and technologies.

#### 📈 Main Panels

- **Project** – directory and file structure.
- **Editor** – main code and layout editor.
- **Logcat** – system and app logs.
- **Build** – build status.
- **Device Manager** – manage emulators and physical devices.

---
## 🔍 Selected Project Details in Android Studio

This section covers several important aspects of working in Android Studio that often appear at the start of learning mobile app development:

- Package name
- Project views in Android Studio vs actual disk structure
- Debugging and Logcat
- Android Emulator

---

### 🏷️ Package Name

The **package name** is a unique identifier for your app in the Android system. It is written in the `AndroidManifest.xml` file and in `build.gradle` as `applicationId`.

#### 📌 Example:

```kotlin
// app/build.gradle
defaultConfig {
    applicationId "com.example.myapp"
    ...
}
```

- **`com.example.myapp`** – an example of the "reverse domain" convention.
- **Package name ≠ directory structure**, but they are usually consistent for readability.
- The package name affects:
  - where the app is installed on the device,
  - app identification in Google Play,
  - generation of the `BuildConfig` file and keys for Firebase.

> 📌 Note: Changing `applicationId` in `build.gradle` allows publishing a different version of the app without colliding with the previous one.

---

### 🗂️ Project Views: Android Studio vs File System

Android Studio offers different **project views** to make work easier:

#### 📁 **Android** View

- Groups files logically (e.g., all layouts together).
- Hides unnecessary folders (`build`, `intermediates`).
- Most commonly used by beginners and in everyday work.

#### 🗃️ **Project** View

- Shows the **actual directory structure on disk**.
- Useful when working with `gradle` files, CI/CD configurations, etc.
- Makes it easier to navigate the project in the file system.

#### 📂 Project Directory Structure

After creating a new project in Android Studio, the default disk structure looks roughly like this:

```
MyApp/
├── app/
│   ├── build.gradle
│   ├── src/
│   │   ├── androidTest/   
│   │   ├── main/
│   │   │   ├── java/
│   │   │   │   └── com/example/myapp/
│   │   │   │       └── MainActivity.kt
│   │   │   ├── res/
│   │   │   │   ├── layout/
│   │   │   │   ├── values/
│   │   │   │   └── drawable/
│   │   │   └── AndroidManifest.xml
│   │   └── test/         
├── build.gradle
├── settings.gradle
├── gradle.properties
└── gradle/
```

In the Android view, a sample structure for a new project is as follows:

```
MyApp (Android)
├── manifests
│   └── AndroidManifest.xml
├── java
│   ├── com.example.myapp
│   │   └── MainActivity.kt
│   └── (Tests and AndroidTest)
├── res
│   ├── drawable/       
│   ├── layout/         
│   ├── mipmap/          
│   ├── values/          
│   └── themes/           
├── Gradle Scripts
│   ├── build.gradle (Project: MyApp)
│   ├── build.gradle (Module: app)
│   ├── settings.gradle
│   └── gradle.properties

```
#### 📝 Description of Some Elements:

- **`manifests/AndroidManifest.xml`**  
  - Declares basic app information: package, activities, permissions.

- **`java/com.example.myapp`**  
  - Main code space for the app. Here are classes and functions (e.g., `MainActivity.kt`, `Repository.kt`, `ViewModel.kt`).
  - You can create subfolders by architecture: `ui`, `data`, `domain`, etc.

- **`res/`**  
  - App resources – images, colors, texts, styles, etc.

- **`Gradle Scripts/`**  
  - Configuration scripts:
    - `build.gradle (Project)` – general project configuration (e.g., plugin versions, repositories).
    - `build.gradle (Module)` – app module configuration (`applicationId`, dependencies, versions).
   

> 💡 The "Android" view does not show the `build/`, `intermediates/`, `outputs/` folders – these are hidden to avoid clutter in daily work.

---

📌 The "Android" view is the most convenient way to browse the project during daily programming, but sometimes it's worth switching to the "Project" view to see the exact file layout on disk.

---


## 📱 Android Emulator

The **Android Emulator** lets you run your app without a physical device. All devices are managed by the `Device Manager`.

### ✨ Advantages:

1. **Fast and convenient testing**
   - Instantly run your app without connecting a physical device.
  
2. **Multiple device configurations**
   - Create many virtual devices (AVD – Android Virtual Devices) with different Android versions, screen sizes, dpi, RAM, etc.
   - Useful for testing UI responsiveness on different device types (e.g., phone, tablet, foldable).

3. **Hardware feature simulation**
   - Simulate:
     - GPS location,
     - phone calls and SMS,
     - battery level,
     - network conditions (e.g., 2G, 3G, Wi-Fi),
     - sensors (accelerometer, gyroscope, etc.),
     - cameras (front and rear),
     - screen rotation.
   - These features are often hard to test on a single physical device.

4. **Better logging and debugging**
   - Easy integration with Android Studio tools: Logcat, Debugger, Profiler.
   - Quick access to system logs and resource inspection.

5. **Safety and isolation**
   - Emulator runs in an isolated environment – you don't risk damaging your physical phone or accidentally overwriting data.

6. **No need to buy multiple devices**
   - Test apps on different Android versions (even older ones) and hardware configurations without investing in real phones or tablets.

7. **Quick reset and data wipe**
   - Easily restore the emulator to its initial state – useful for onboarding, permissions, or first-launch tests.

### ❌ Disadvantages of the Emulator:

1. **High resource usage**
   - The emulator uses a significant amount of RAM, CPU, and disk space.
   - On weaker computers, it can noticeably reduce system performance.

2. **Slower operation compared to physical devices**
   - Even with hardware acceleration, the emulator may run less smoothly.
   - App startup times can be longer.

3. **Problems with hardware feature support**
   - Not all hardware components are fully simulated (e.g., NFC, Bluetooth, sensors, camera).
   - Functionality may differ from a real device.

---
## 🐞 Debugging and Logcat in Android Studio

Debugging is the process of finding and removing bugs in code. Android Studio offers a rich set of debugging tools, with the most important being **Logcat** and the **debugger with breakpoints**.

---

### 🧪 Debugging Apps in Android Studio

Android Studio lets you debug your app step by step, pause execution at specific points, and inspect variable values and the call stack.

#### 🔹 Breakpoint

- A breakpoint is a stop point – where the debugger pauses program execution.
- Set it by clicking the left margin of the code editor next to a line.
- Lets you check the app state at that moment: variable values, method calls, etc.

#### 🔹 Debugger

- To run the app in debug mode, click the bug icon ("Debug app").
- When stopped at a breakpoint, you can inspect variables, registers, and the call stack.

---

## 📋 Logcat – Error Analysis and Exception Diagnostics

**Logcat** is the main diagnostic tool in Android Studio – it allows you to track system and app logs in real time. With it, you can easily spot exceptions, error messages, warnings, or system actions.

---

### 🔹 Logging Levels

| Level | Meaning                   |
|-------|---------------------------|
| `V`   | Verbose – all logs        |
| `D`   | Debug – developer logs    |
| `I`   | Info – general info       |
| `W`   | Warn – warnings           |
| `E`   | Error – critical errors   |
| `A`   | Assert – critical asserts |

---

### 🔹 Logging Example in Kotlin:

```kotlin
import android.util.Log

Log.d("LoginScreen", "Login button clicked")
Log.w("LoginScreen", "No connection to server")
Log.e("LoginScreen", "Authorization error", exception)
```

---

### 🧨 Exception Example in Logcat

Suppose the app throws a `NullPointerException`. In Logcat, you'll see something like:

```bash
E/AndroidRuntime: FATAL EXCEPTION: main
    Process: com.example.myapp, PID: 31548
    java.lang.NullPointerException: Attempt to invoke virtual method 'java.lang.String android.widget.TextView.getText()' on a null object reference
        at com.example.myapp.ui.MainActivity.onCreate(MainActivity.kt:25)
        at android.app.Activity.performCreate(Activity.java:7893)
        ...
```

---

### 🧠 How to Analyze an Exception?

1. **Exception type**: `NullPointerException`  
   ➤ This means the app tried to access an object that was `null`.

2. **Error description**:
   ```
   Attempt to invoke virtual method 'java.lang.String android.widget.TextView.getText()' on a null object reference
   ```
   ➤ Called `getText()` on an uninitialized `TextView` object.

3. **Error location**:
   ```
   at com.example.myapp.ui.MainActivity.onCreate(MainActivity.kt:25)
   ```
   ➤ The error is in the `MainActivity` class, line 25.

---

### 🔧 Typical Code That Could Cause This Exception:

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)

    val text = findViewById<TextView>(R.id.myTextView)
    val input = text.text.toString() // <- crash if `text` is null
}
```
---

### 🔍 Filtering Logcat:

To quickly find an error or information in Logcat:
- Select the appropriate logging level, e.g., **Error**
- Enter the app name or tag in the filter, e.g.: `com.example.myapp`, `LoginScreen` 

---

### 🧪 Debugging + Logcat = Full Analysis

After finding an error in Logcat, it's worth:
- Setting a **breakpoint** on the suspicious line
- Running the app in debug mode and analyzing the code

---

📌 **Tip**: Logcat works best when you regularly log important information in your code – not just errors, but also app flow (e.g., screen transitions, user input, API responses).

---

### 🧰 Other Debugging Tools:

- **Layout Inspector** – for analyzing view structure in real time.
- **Network Profiler** – view network requests.
- **Memory Profiler** – monitor memory usage (especially for memory leaks).
- **CPU Profiler** – analyze app performance in real time.

---

## ⚙️ Gradle – Build System

**Gradle** is the automation system Android Studio uses for:

- building the app,
- managing dependencies (e.g., Jetpack libraries),
- creating different versions (e.g., debug/release),
- running tests,
- configuring production and test environments.

### 🧠 How Does Gradle Work?

- Gradle analyzes configuration files (`build.gradle`) and creates a **task graph**.
- Executes tasks depending on the goal (e.g., `build`, `assembleDebug`).
- Downloads dependencies from repositories like **Google** or **Maven Central**.
- Thanks to **Kotlin DSL (`.gradle.kts`)**, you can alternatively configure in Kotlin.

---

## 📄 Example Gradle Files

### 🔤 `build.gradle` (main project)

This file configures global settings for all project modules (e.g., apps, libraries, etc.).

```groovy
buildscript {
    ext {
        kotlin_version = '1.9.22'
    }
    repositories {
        google()
        mavenCentral()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:8.4.0'
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
}
```

### 🔤 `build.gradle` (app module)

Contains information about building your app, including:

- plugins,
- SDK levels,
- dependencies,
- build and test options.

#### 📌 Example:

```groovy
plugins {
    id 'com.android.application'
    id 'org.jetbrains.kotlin.android'
}

android {
    compileSdk 34

    defaultConfig {
        applicationId "com.example.myapp"
        minSdk 24
        targetSdk 34
        versionCode 1
        versionName "1.0"
    }

    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
    }
}

dependencies {
    implementation 'androidx.core:core-ktx:1.13.0'
    implementation 'androidx.compose.ui:ui:1.6.4'
}
```
## 🧱 Sections of the `build.gradle` App Module File (`app/`)

The `app/build.gradle` (or `build.gradle.kts`) file contains configuration for a specific module – for Android, this is usually the app module. Below are key sections of this file.

---

### 🔌 `plugins`

Declares plugins needed to build this module. For Android apps and Kotlin, these are usually:

```groovy
plugins {
    id 'com.android.application'
    id 'org.jetbrains.kotlin.android'
}
```

---

### 🧱 `android`

This is the main section configuring the Android project. Inside, you'll find several subsections:

```groovy
android {
    namespace 'com.example.myapp' // since Android Studio Flamingo, replaces applicationId
    compileSdk 34

    defaultConfig {
        applicationId "com.example.myapp"  // unique app identifier (package name)
        minSdk 24
        targetSdk 34
        versionCode 1
        versionName "1.0"
    }

    buildTypes {
        release {
            minifyEnabled false  // Proguard/R8 – compression and obfuscation
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
        }
        debug {
            applicationIdSuffix ".debug" // debug app has a different package name
            debuggable true
        }
    }

    buildFeatures {
        compose true  // enables Jetpack Compose
    }

    composeOptions {
        kotlinCompilerExtensionVersion '1.6.4'
    }

    
}
```

#### 📌 Subsection Descriptions:

| Section                | Description |
|------------------------|-------------|
| `namespace`            | Declares the namespace for classes (replaces `applicationId` in newer projects). |
| `compileSdk`           | SDK version used to compile the app. |
| `defaultConfig`        | Default app settings (ID, version, SDK, tests). |
| `buildTypes`           | Defines build types (debug, release). |
| `buildFeatures`        | Enables selected features like Compose, ViewBinding, DataBinding. |
| `composeOptions`       | Jetpack Compose-specific settings. |

---

### 📦 `dependencies`

Section containing all dependencies – external libraries, AndroidX, Compose, Retrofit, Firebase, etc.

```groovy
dependencies {
    implementation 'androidx.core:core-ktx:1.13.0'
    implementation 'androidx.lifecycle:lifecycle-runtime-ktx:2.7.0'
    implementation 'androidx.compose.ui:ui:1.6.4'
    implementation 'com.google.dagger:hilt-android:2.51'

    testImplementation 'junit:junit:4.13.2'
    androidTestImplementation 'androidx.test.ext:junit:1.1.5'
    debugImplementation 'androidx.compose.ui:ui-tooling:1.6.4'
}
```

#### Dependency Types:

| Type                   | Meaning |
|------------------------|---------|
| `implementation`       | Main dependency – visible only in this module. |
| `testImplementation`   | Used only for unit tests. |
| `androidTestImplementation` | For instrumented tests. |
| `debugImplementation`  | Used only in debug builds (e.g., UI tooling). |

---

📌 Tip: Android Studio automatically syncs the project after changes in Gradle files (required). If sync doesn't happen automatically, use the **"Sync Now"** button.

---

## 🧩 Plugins vs Dependencies – What's the Difference?

| Feature            | Plugins                                         | Dependencies                                 |
|--------------------|------------------------------------------------|----------------------------------------------|
| What is it?        | Gradle extensions that modify build behavior    | External code you add to your app            |
| Example            | `com.android.application`, `kotlin-android`     | `androidx.compose.ui:ui`, `retrofit2:retrofit`|
| Where to add       | `plugins` section                               | `dependencies` section                       |
| Function           | Changes project configuration                   | Adds functionality to the app (e.g., database)|

---
## 📦 Modern Version Management: `libs.versions.toml`

Since Gradle 7.0, you can use the `libs.versions.toml` file for centralized management of library and plugin versions.

### 🔧 Location:

```
MyApp/
└── gradle/
    └── libs.versions.toml
```

### 📄 Sample `libs.versions.toml` Content:

```toml
[versions]
kotlin = "1.9.22"
compose = "1.6.4"
hilt = "2.51"

[libraries]
kotlin-stdlib = { module = "org.jetbrains.kotlin:kotlin-stdlib", version.ref = "kotlin" }
compose-ui = { module = "androidx.compose.ui:ui", version.ref = "compose" }
hilt-android = { module = "com.google.dagger:hilt-android", version.ref = "hilt" }

[plugins]
android-application = { id = "com.android.application", version = "8.4.0" }
kotlin-android = { id = "org.jetbrains.kotlin.android", version.ref = "kotlin" }
```

### 📄 build.gradle.kts Using `libs.versions.toml`

```kotlin
plugins {
    alias(libs.plugins.android.application)
    alias(libs.plugins.kotlin.android)
}

dependencies {
    implementation(libs.kotlin.stdlib)
    implementation(libs.compose.ui)
}
```

---

✅ **Advantages of `libs.versions.toml`**:
- central place for version management,
- no duplication of versions between modules,
- easy dependency updates.
---


✅ **Check the official documentation** [Android Studio](https://developer.android.com/studio) and [Gradle](https://docs.gradle.org/current/userguide/userguide.html).


### 🧭 **Next topic:** [Activity concept and lifecycle](https://github.com/MarcinRod/AndroidLecture2025EN/blob/main/03%20Activity.md)
