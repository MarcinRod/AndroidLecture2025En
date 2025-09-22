# 🚀 Kotlin Basics for Android

Kotlin is a modern, statically typed programming language officially supported by Google for Android app development. It is readable, concise, and fully interoperable with Java.

---

## 🔹 Key Features of Kotlin

- **Conciseness** – less code than Java, no unnecessary getters/setters.
- **Null safety** – the type system prevents many `null`-related errors.
- **Higher-order functions and lambdas** – easily pass functions as parameters.
- **Extensions** – add new functions to existing classes.
- **Java interoperability** – you can use Java libraries and gradually migrate code.

---

## 📦 Basic Kotlin Syntax

### Variables and Constants

In Kotlin, you use two keywords to store data: `val` and `var`.

- **val** – declares constants (the value cannot be changed after assignment). Equivalent to read-only variables.
- **var** – declares variables (the value can be changed freely during program execution).

**Examples:**

  ```kotlin
    val pi = 3.14           // constant, cannot assign a new value
    var counter = 0         // variable, value can be changed

    counter = 5             // OK
    // pi = 3.1415          // Compilation error!
  ```

- The variable type is usually inferred automatically, but you can specify it explicitly:
  ```kotlin
  val name: String = "Android"
  var age: Int = 20
  ```

- You can declare a variable without initialization, but then you must specify the type:
  ```kotlin
  var result: Int
  result = 42
  ```

**Best practices:**
- Use `val` whenever you don't need to change the value – the code is safer and more readable.
- Use `var` only when the variable's value actually changes.

Variables and constants in Kotlin are fundamental for working with data in Android applications.

---

## 🔹 Basic Data Types

- `Int`, `Long`, `Double`, `Float` – numbers
- `Boolean` – logical values (`true`/`false`)
- `String` – text
- `Char` – single character
- `List`, `MutableList`, `Set`, `Map` – collections

```kotlin
val number: Int = 42
val text: String = "Hello"
val list: List<String> = listOf("A", "B", "C")
```

---

## 🔸 Conditional Expressions

- **if / else** – as in Java, but can also be an expression (returns a value)
- **when** – extended version of switch

```kotlin
val x = 10
val result = if (x > 5) "large" else "small"

when (x) {
    1 -> println("one")
    in 2..10 -> println("from 2 to 10")
    else -> println("other")
}
```

---

## 🔸 String Handling

- **Concatenation:** You can concatenate strings using the `+` operator:
  ```kotlin
  val name = "Jan"
  val greeting = "Hello, " + name + "!"
  ```

- **Interpolation:** Most commonly used – insert variable values directly into text using `$`:
  ```kotlin
  val name = "Jan"
  val greeting = "Hello, $name!"
  val age = 20
  val info = "You are ${age + 1} years old"
  ```

- **Multiline strings:** Use triple quotes `""" ... """` to create texts with multiple lines or special characters without escaping:
  ```kotlin
  val multiline = """
      This is
      text in several lines
      Tab character:    <- here
  """.trimIndent()
  ```

- **Basic string operations:**
  - `length` – text length: `val length = text.length`
  - `uppercase()`, `lowercase()` – change case: `text.uppercase()`
  - `substring()` – extract part: `text.substring(0, 3)`
  - `replace()` – replace part: `text.replace("a", "b")`
  - `contains()` – check if text contains substring: `text.contains("cat")`
  - `split()` – split into parts: `"a,b,c".split(",")`
  - `trim()` – remove whitespace from start and end: `text.trim()`

- **String comparison:**
  - Standard comparison: `a == b`
  - Case-insensitive comparison: `a.equals(b, ignoreCase = true)`

- **Convert other types to string:**
  ```kotlin
  val number = 123
  val text = number.toString()
  ```

---

## 🔸 Loops

Kotlin supports various types of loops, as well as convenient ranges:

- **For loop with range:**
  ```kotlin
  for (i in 1..5) { // from 1 to 5 inclusive
      println(i)
  }
  for (i in 5 downTo 1) { // from 5 down to 1
      println(i)
  }
  for (i in 1 until 5) { // from 1 to 4 (5 excluded)
      println(i)
  }
  for (i in 0..10 step 2) { // every 2
      println(i)
  }
  ```

- **For loop over a collection:**
  ```kotlin
  val list = listOf("A", "B", "C")
  for (element in list) {
      println(element)
  }
  ```

- **While loop:**
  ```kotlin
  var counter = 0
  while (counter < 3) {
      println(counter)
      counter++
  }
  ```

### Ranges

- Created using the `..` operator (e.g., `1..10`).
- You can use `downTo`, `until`, `step` to control the range.
- Ranges are often used in loops, but also in conditions (`if (x in 1..10)`).

**Example of using a range in a condition:**
```kotlin
val age = 18
if (age in 13..19) {
    println("Teenager")
}
```
---

### Functions

Functions in Kotlin are the basic way to organize code. They allow you to reuse logic, pass parameters, and return values.

```kotlin
fun add(a: Int, b: Int): Int {
    return a + b
}

// Function as an expression (short form):
fun greet(name: String) = "Hello, $name!"
```

#### Types of function arguments

- **Default arguments:** You can assign a default value to a parameter.
  ```kotlin
  fun greeting(name: String = "Guest") {
      println("Hello, $name!")
  }
  greeting() // Hello, Guest!
  greeting("Anna") // Hello, Anna!
  ```

- **Named arguments:** You can pass arguments by name, which increases readability. You also don't have to worry about argument order.
  ```kotlin
  fun order(name: String, quantity: Int, urgent: Boolean = false) { /* ... */ }
  order(name = "Coffee", quantity = 2, urgent = true)
  ```

- **Vararg arguments:** Allow you to pass any number of arguments of the same type.
  ```kotlin
  fun sum(vararg numbers: Int): Int = numbers.sum()
  val result = sum(1, 2, 3, 4) // 10
  ```

#### Function naming

- Function names should be verbs in the indicative mood, e.g., `add`, `sendEmail`, `fetchData`.
- Functions returning a boolean often start with `is`, `has`, `can`, e.g., `isActive()`, `hasPermission()`, `canEdit()`.
- Names should be short but descriptive.

---

### Lambda Functions

Lambda functions are short, anonymous functions that you can assign to a variable or pass as an argument to another function.
- **Basic lambda syntax:**

    ```kotlin
    { parameters -> body }
    { x: Int -> x*2 }
    ```

- **Lambda without parameters:**
  ```kotlin
  val greeting = { println("Hello!") }
  greeting() // prints: Hello!
  ```

- **Lambda with one parameter:**
  ```kotlin
  val double = { x: Int -> x * 2 }
  println(double(5)) // prints: 10
  ```

- **Lambda as a function argument:**
  ```kotlin
  fun execute(action: () -> Unit) {
      action()
  }

  execute { println("This is a lambda!") }
  ```

### Collections

Kotlin offers convenient and safe collections, widely used in Android apps. The most commonly used collection types are:

- **List** – read-only list (immutable)
- **MutableList** – list that can be modified (add, remove elements)
- **Set** / **MutableSet** – set of unique elements
- **Map** / **MutableMap** – key-value pair

**Declaration examples:**
```kotlin
val list = listOf("A", "B", "C")                // immutable list
val mutableList = mutableListOf(1, 2, 3)         // mutable list
val set = setOf("cat", "dog", "cat")          // set (duplicates ignored)
val map = mapOf("key" to 1, "second" to 2)     // read-only map
val mutableMap = mutableMapOf("a" to 1, "b" to 2)
```

**Basic collection operations:**
- Read element from list: `list[0]`
- Add element to mutable list: `mutableList.add(4)`
- Remove element: `mutableList.remove(2)`
- Check if list contains element: `list.contains("A")`
- Iterate over collection:
  ```kotlin
  for (element in list) {
      println(element)
  }
  ```

**Lambda expressions and collections:**
Kotlin allows convenient processing of collections using higher-order functions and lambdas, e.g.:
```kotlin
val numbers = listOf(1, 2, 3, 4, 5)
val even = numbers.filter { it % 2 == 0 }      // [2, 4]
val doubled = numbers.map { it * 2 }           // [2, 4, 6, 8, 10]
val sum = numbers.sum()                        // 15
```

**Practical example:**
```kotlin
val products = listOf("Coffee", "Tea", "Juice")
products.forEach { product ->
    println("Product: $product")
}
```

**Tips:**
- By default, collections are immutable – if you need to modify a collection, use `MutableList`, `MutableSet`, or `MutableMap`.


### Null safety

Null safety is one of the most important features of Kotlin, helping to avoid errors related to `null` values (NullPointerException).

- **By default, variables cannot be null:**
  ```kotlin
  var text: String = "Hello"
  text = null // Compilation error!
  ```

- **To allow a variable to be null, add a question mark to the type:**
  ```kotlin
  var text: String? = null
  text = "Hello"
  ```

- **Safe call operator (`?.`):**
  Allows you to call a method or access a property only if the object is not null.
  ```kotlin
  println(text?.length) // if text == null, result is null, no error
  ```

- **Elvis operator (`?:`):**
  Allows you to set a default value when the variable is null.
  ```kotlin
  val length = text?.length ?: 0 // if text == null, length = 0
  ```

- **Force non-null (`!!`):**
  Throws an exception if the variable is null (use only when you are sure it won't be null).
  ```kotlin
  val length = text!!.length // if text == null, the app will crash
  ```

- **Safe cast (`as?`):**
  Returns null if the cast fails.
  ```kotlin
  val number: Int? = "123".toIntOrNull()
  ```

- **Practical example:**
  ```kotlin
  fun showLength(text: String?) {
      println("Length: ${text?.length ?: "no text"}")
  }
  showLength("Kotlin") // Length: 6
  showLength(null)     // Length: no text
  ```

**Tips:**
- Try to avoid nullable variables unless necessary.
- Null safety makes code safer and easier to maintain, which is especially important in Android apps.

---

### Classes

In Kotlin, classes are the basic way to define your own data types.

#### Class naming

- Class names in Kotlin should use **PascalCase** – each word starts with a capital letter, no underscores, e.g., `User`, `MainActivity`, `ProductItem`.
- The class name should clearly describe what the class represents (e.g., `User`, `Order`, `LoginViewModel`).
- For data classes (`data class`), use the same rules – the name should be a noun.
- Avoid abbreviations and unclear names – code should be self-describing.
- For singleton objects (`object`) and companion objects, also use PascalCase, e.g., `Logger`, `DatabaseHelper`.

**Good naming examples:**
```kotlin
class UserProfile
data class ProductItem(val name: String, val price: Double)
object NetworkManager
```
**Bad naming examples:**
```kotlin
class userprofile
class product_item
object networkmanager
```
Good class naming makes code easier to read, test, and maintain in larger Android projects.

- **Regular class:**
  ```kotlin
  class Person(val name: String, var age: Int)
  val john = Person("John", 30)
  println(john.name) // John
  john.age = 31
  ```

- **Class with methods:**
  ```kotlin
  class Calculator {
      fun add(a: Int, b: Int): Int = a + b
  }
  val c = Calculator()
  println(c.add(2, 3)) // 5
  ```
#### Class constructor

In Kotlin, the constructor is a special function used to create objects of a class. Most often, you use the **primary constructor**, defined directly in the class header:

```kotlin
class Person(val name: String, var age: Int)
```
- Constructor parameters can immediately become class properties (with `val` or `var`), as in the example above.
- Creating an object:
  ```kotlin
  val john = Person("John", 30)
  ```

You can also define a **secondary constructor** if you need another way to create an object:

```kotlin
class Person(val name: String) {
    var age: Int = 0

    constructor(name: String, age: Int) : this(name) {
        this.age = age
    }
}
```

- The secondary constructor uses the `constructor` keyword and must always call the primary constructor (`: this(...)`).

**Important:**
- If a class has no properties or methods, you can omit the parentheses:
  ```kotlin
  class Empty
  ```
- If you want to perform additional logic when creating an object, use the `init` block:
  ```kotlin
  class Person(val name: String) {
      init {
          println("Creating person named $name")
      }
  }
  ```

### Inheritance

In Kotlin, you can create class hierarchies and use inheritance to reuse code and extend functionality.

- By default, every class in Kotlin is **final** (cannot be inherited). To allow inheritance, use the `open` keyword for class and method definitions.

**Inheritance example:**
```kotlin
open class Animal(val name: String) {
    open fun makeSound() {
        println("Animal makes a sound")
    }
}

class Dog(name: String) : Animal(name) {
    override fun makeSound() {
        println("Woof woof!")
    }
}

val dog = Dog("Rex")
dog.makeSound() // Woof woof!
```

- **open** – allows inheritance of the class or overriding of the method.
- **override** – used to override methods from the base class.
- The derived class constructor calls the base class constructor using `: Base(...)`.

**Interfaces and inheritance:**

Interfaces in Kotlin are defined using the `interface` keyword. An interface can contain method declarations (without implementation) and default method implementations.

- A class can implement any number of interfaces (unlike class inheritance, which is single).
- Interfaces are often used to define contracts that must be fulfilled by classes (e.g., handling clicks, communication between components).

**Simple interface example:**
```kotlin
interface Clickable {
    fun click()
}

class Button : Clickable {
    override fun click() {
        println("Button clicked")
    }
}

val button = Button()
button.click() // Button clicked
```

**Interface with default implementation:**
```kotlin
interface Welcoming {
    fun welcome(name: String) {
        println("Hello, $name!")
    }
}

class User : Welcoming

val user = User()
user.welcome("Anna") // Hello, Anna!
```

**Implementing multiple interfaces:**
```kotlin
interface Clickable { fun click() }
interface Flyable { fun fly() }

class SuperBird : Clickable, Flyable {
    override fun click() { println("Bird clicked") }
    override fun fly() { println("Bird flies") }
}
```

**Important features of interfaces in Kotlin:**
- An interface can have properties (without state), e.g., `val name: String`
- An interface cannot store state (cannot have fields with values)
- You can implement multiple interfaces in one class

Interfaces are widely used in Android, e.g., for event handling (clicks, callbacks), communication between fragments, adapters, etc.

## Special Classes

- **Data class** – a special class type for storing data. Automatically generates `equals()`, `hashCode()`, `toString()`, `copy()`, and `componentN()` methods:
  ```kotlin
  data class Product(val name: String, val price: Double)
  val coffee = Product("Coffee", 12.99)
  println(coffee) // Product(name=Coffee, price=12.99)
  val newCoffee = coffee.copy(price = 10.99)
  ```

- **enum class** – enumeration type, allows you to define a limited set of constant values. Useful for representing states, types, categories:
  ```kotlin
  enum class Status {
      LOADING, SUCCESS, ERROR
  }

  val status = Status.SUCCESS
  when (status) {
      Status.LOADING -> println("Loading...")
      Status.SUCCESS -> println("Success!")
      Status.ERROR -> println("An error occurred")
  }
  ```
    Enums in Kotlin can have their own properties, methods, and constructors. This allows enums to store additional data and behavior.

    ```kotlin
    enum class OrderState(val description: String, val isFinal: Boolean) {
        NEW("New order", false),
        IN_PROGRESS("In progress", false),
        COMPLETED("Completed", true),
        CANCELLED("Cancelled", true);
    }
    ```
- **object** – keyword for creating singletons (single instances of classes) or anonymous objects.

- **Singleton:**
  ```kotlin
  object Logger {
      fun log(msg: String) {
          println("LOG: $msg")
      }
  }
  Logger.log("App started")
  ```

- **Anonymous object** – useful for implementing interfaces "on the fly":
  ```kotlin
  val clicker = object : Clickable {
      override fun click() {
          println("Anonymous object clicked")
      }
  }
  clicker.click()
  ```

- **object companion** – companion object in a class, allows creating static methods/fields:
  ```kotlin
  class User(val name: String) {
      companion object {
          fun createAnonymous() = User("Anonymous")
      }
  }
  val anonymous = User.createAnonymous()
  ```

---

## 🔸 Extension Functions and Properties

Extension functions and properties allow you to add new methods or properties to existing classes – even those you cannot modify (e.g., classes from libraries or Java).

### Extension functions

- Define them outside the class, prefixing with the type you want to extend:
  ```kotlin
  fun String.reverse(): String = this.reversed()

  val text = "Kotlin"
  println(text.reverse()) // "niltok"
  ```

- You can extend any type, including Android classes:
  ```kotlin
  fun Context.toast(msg: String) =
      Toast.makeText(this, msg, Toast.LENGTH_SHORT).show()
  ```

- Extension functions have access to public methods and properties of the extended type via `this`.

- Extension functions do not modify the original class.

### Extension properties

- Allow you to add "pseudo-properties" to existing classes:
  ```kotlin
  val String.reversedText: String
      get() = this.reversed()

  println("Android".reversedText) // "diordnA"
  ```

- Extension properties cannot have state (you cannot declare a field), only a getter.




**Summary:**
- Extension functions and properties improve code readability and allow you to write more idiomatic APIs.
- They do not modify original classes – they are safe and convenient.

---

## 📚 Documentation and Resources

- [Official Kotlin Documentation](https://kotlinlang.org/docs/home.html)
- [Kotlin for Android Developers](https://developer.android.com/kotlin)
- [Kotlin Basics on Android Developers](https://developer.android.com/kotlin/learn)
---
### 🧭 **Next topic:** [Android Studio](https://github.com/MarcinRod/AndroidLecture2025EN/blob/main/02%20Android%20Studio.md)
