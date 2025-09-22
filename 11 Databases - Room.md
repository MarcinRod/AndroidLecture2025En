# Databases in Android – Room Library

Room is the official Jetpack library that simplifies working with relational SQLite databases in Android apps. It provides a safe and convenient way to save and read data.

---

## Why Room Library?

- Provides an abstraction layer over SQLite.
- Automatically generates SQL code based on annotations.
- Enables type-safe queries and database migrations.
- Integrates with coroutines, Flow, and StateFlow.

---

## Core Elements of Room

1. **Entity**  
   A data class annotated with `@Entity` that represents a table in the database. Each field in the class corresponds to a column in the table.  
   The **primary key** is annotated with `@PrimaryKey`. It can be a single field (e.g., `id`), which can be auto-generated (`autoGenerate = true`), or you can define a composite key (specifying multiple fields as the primary key). The primary key guarantees the uniqueness of each record in the table and is required by Room.

   **Example:**
   ```kotlin
   @Entity
   data class Product(
       @PrimaryKey(autoGenerate = true) val id: Int = 0, // primary key, auto-increment
       val name: String,
       val price: Double
   )
   ```

   You can also define a primary key without auto-increment:
   ```kotlin
   @Entity
   data class User(
       @PrimaryKey val email: String, // primary key is email
       val firstName: String
   )
   ```

2. **DAO (Data Access Object)**  
   An interface annotated with `@Dao`. Contains methods for database operations: insert (`@Insert`), update (`@Update`), delete (`@Delete`), and queries (`@Query`). Methods can be marked as `suspend` (for coroutines) or return `Flow` (for observing changes).

   - **@Insert**  
     The `@Insert` annotation is used to insert new records into the table. Room automatically generates the appropriate SQL command based on the object or list of objects provided.  
     You can specify a conflict strategy (e.g., `OnConflictStrategy.REPLACE`), which decides what to do if you try to insert a record with an existing primary key.

     **Example:**
     ```kotlin
     @Insert(onConflict = OnConflictStrategy.REPLACE)
     suspend fun insert(product: Product)
     ```

   - **@Query**  
     The `@Query` annotation lets you define custom SQL queries (e.g., SELECT). Room checks the query for correctness at compile time and automatically maps results to Kotlin objects.  
     You can pass parameters to the query using the `:parameterName` syntax.

     **Example:**
     ```kotlin
     @Query("SELECT * FROM Product")
     suspend fun getAll(): List<Product>

     @Query("SELECT * FROM Product WHERE name = :name")
     suspend fun findByName(name: String): List<Product>
     ```
     **Example using Flow:**
     ```kotlin
     @Query("SELECT * FROM Product")
     fun observeAll(): Flow<List<Product>>
     ```

     **Advantage of using Flow:**  
     A method returning `Flow` allows you to **observe changes in the database in real time**. Whenever data in the table changes (e.g., a new product is added), all composables or ViewModels subscribing to this `Flow` automatically receive the updated product list without manual data refresh.

3. **Database**  
   An abstract class extending `RoomDatabase`, annotated with `@Database`. You specify the list of entities (`entities`) and the database version (`version`). Must contain abstract methods returning DAOs for each entity you want to use.

   - You do not implement any methods in this class—Room generates all database access logic based on DAO declarations.
   - The database class should be a singleton in the app (created only once).

   ##### **General process for creating a RoomDatabase instance:**
   1. Create a public abstract class that extends `RoomDatabase`. The class is abstract because Room will generate its implementation.
   2. Annotate the class with `@Database`. In the arguments, list the entities for the database and set the version number.
   3. Define an abstract method or property that returns the DAO instance.
   4. You need only one instance of `RoomDatabase` for the whole app, so it should be a singleton.
   5. To create the database, use `Room.databaseBuilder`, but only if it doesn't exist. Otherwise, return the existing database instance.

   **Example:**
   **Creating a database instance (using companion object):**
   ```kotlin
   @Database(entities = [Product::class], version = 1)
   abstract class AppDatabase : RoomDatabase() {
       abstract fun productDao(): ProductDao

       companion object {
           @Volatile
           private var INSTANCE: AppDatabase? = null

           fun getInstance(context: Context): AppDatabase {
               return INSTANCE ?: synchronized(this) {
                   val instance = Room.databaseBuilder(
                       context.applicationContext,
                       AppDatabase::class.java,
                       "products.db"
                   ).build()
                   INSTANCE = instance
                   instance
               }
           }
       }
   }
   ```
   With the companion object, you ensure that there is only one database instance (singleton) throughout the app.

The `@Volatile` annotation in Kotlin (and Java) means that the variable can be shared between different threads and its value will always be up-to-date for all threads.

With `@Volatile`:
- Every thread always sees the latest value of the `INSTANCE` variable.
- Prevents situations where two threads create two different database instances.
- Important for implementing the singleton pattern in a multithreaded environment.

`synchronized` is a keyword in Kotlin and Java that allows synchronized access to a code block by multiple threads. Only one thread at a time can execute the code in the `synchronized` block.

---

**Additional Room Elements:**

- **TypeConverter** – allows you to save types in the database that are not natively supported by SQLite (e.g., lists, dates).

---

## Example: Saving Data with Room

### 1. Entity Definition

```kotlin
import androidx.room.Entity
import androidx.room.PrimaryKey

@Entity
data class Product(
    @PrimaryKey(autoGenerate = true) val id: Int = 0,
    val name: String,
    val price: Double
)
```

### 2. DAO – Data Access Interface

```kotlin
import androidx.room.*

@Dao
interface ProductDao {
    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun insert(product: Product)

    @Query("SELECT * FROM Product")
    suspend fun getAll(): List<Product>

    @Query("SELECT * FROM Product")
    fun observeAll(): Flow<List<Product>>
}
```

### 3. Database Class (singleton with companion object)

```kotlin
import android.content.Context
import androidx.room.Database
import androidx.room.Room
import androidx.room.RoomDatabase

@Database(entities = [Product::class], version = 1)
abstract class AppDatabase : RoomDatabase() {
    abstract fun productDao(): ProductDao

    companion object {
        @Volatile
        private var INSTANCE: AppDatabase? = null

        fun getInstance(context: Context): AppDatabase {
            return INSTANCE ?: synchronized(this) {
                val instance = Room.databaseBuilder(
                    context.applicationContext,
                    AppDatabase::class.java,
                    "products.db"
                ).build()
                INSTANCE = instance
                instance
            }
        }
    }
}
```

### 4. Getting DAO and Saving Data (e.g., in ViewModel)

```kotlin
// In ViewModel or repository:
val db = AppDatabase.getInstance(context)
val productDao = db.productDao()

viewModelScope.launch {
    val newProduct = Product(name = "Coffee", price = 12.99)
    productDao.insert(newProduct)
}
```

### 5. Observing Database Changes (e.g., in ViewModel)

```kotlin
val productsFlow: Flow<List<Product>> = productDao.observeAll()
```

---

**Tips:**
- Always perform database operations in coroutines (`suspend fun` or `Flow`) to avoid blocking the main thread.
- Use `Flow` to observe database changes.

**More information:**  
- [Official Room documentation](https://developer.android.com/training/data-storage/room)
- [Room Persistence Library – guide](https://developer.android.com/jetpack/androidx/releases/room)

---

### 🧭 **Next topic:** [Internet Communication - Retrofit](https://github.com/MarcinRod/AndroidLecture2025EN/blob/main/12%20Internet%20Communication%20-%20Retorfit.md)
