# Internet Communication in Android

Modern mobile apps often communicate with servers over the Internet to fetch or send data (e.g., downloading product lists, user login, data synchronization). In Android, the most common libraries for this are **Retrofit** and **OkHttp**.

---

## Permission for Internet Access

To allow your app to communicate with a server over the Internet, you must add the appropriate permission in `AndroidManifest.xml`:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Without this permission, no network request (e.g., via Retrofit, OkHttp) will work, and the app will get a connection error.

**Important:**  
This permission is required for any app that uses the Internet—for both downloading and uploading data.

---

## Key Concepts

- **API (Application Programming Interface)** – a set of rules and addresses (endpoints) through which the app communicates with the server.
- **REST API** – the most popular communication style, based on HTTP and operations like GET, POST, PUT, DELETE.
- **JSON** – the most commonly used data exchange format between app and server.

---

## Popular Communication Libraries

- **Retrofit** – the most popular library for handling REST APIs in Android. Makes it easy to perform HTTP requests and map responses to Kotlin/Java objects.
- **OkHttp** – a low-level HTTP library that Retrofit is built on.

---
## Retrofit – Basics and Example with Moshi Converter

**Retrofit** is the most popular library for REST API communication in Android. It allows you to easily perform HTTP requests and map JSON responses to Kotlin/Java objects. Retrofit supports various JSON converters, such as GSON or Moshi.

---

### Adding Dependencies (build.gradle)

```groovy
implementation "com.squareup.retrofit2:retrofit:2.9.0"
implementation "com.squareup.retrofit2:converter-moshi:2.9.0"
implementation "com.squareup.moshi:moshi:1.15.0"
implementation "com.squareup.moshi:moshi-kotlin:1.15.0"
```

---

### Data Model Definition

```kotlin
import com.squareup.moshi.Json
import com.squareup.moshi.JsonClass

@JsonClass(generateAdapter = true)
data class Product(
    val id: Int,
    val name: String,
    val price: Double
)
```

#### Moshi Annotation Explanation

- **@JsonClass(generateAdapter = true)**  
  Tells Moshi to automatically generate an adapter for serializing and deserializing this class (i.e., converting Kotlin objects to JSON and vice versa). Required for Moshi to handle Kotlin data classes correctly.

- **@Json(name = "field_name")**  
  Maps a class property to a different field name in JSON if they differ.

**Example using @Json:**
```kotlin
@JsonClass(generateAdapter = true)
data class Product(
    val id: Int,
    @Json(name = "product_name") val name: String,
    val price: Double
)
```
In this example, the `name` property in Kotlin will be mapped to the `product_name` field in JSON.

#### Handling Optional Fields in Moshi Objects

If a field in JSON can be optional (may not appear or be `null`), mark it as nullable (`?`) in the Kotlin data model and (optionally) set a default value.

**Example:**
```kotlin
@JsonClass(generateAdapter = true)
data class Product(
    val id: Int,
    val name: String,
    val price: Double,
    val description: String? = null // optional field, can be null if missing in JSON
)
```

- If the `description` field is missing in the server response, Moshi will automatically set it to `null`.
- You can also set another default value, e.g., `val description: String? = "no description"`.

**Important:**  
If a field in the model is not marked as `?` and has no default value, and is missing in JSON, Moshi will throw a deserialization error.

---

### API Interface Definition

The API interface in Retrofit is where you declare methods corresponding to HTTP requests you want to send to the server. Each method is described with the appropriate annotation (e.g., `@GET`, `@POST`, `@PUT`, `@DELETE`) and can take parameters to be passed to the request.

**Key Retrofit annotations:**
- `@GET("endpoint")` – GET request (fetch data).
- `@POST("endpoint")` – POST request (send data).
- `@PUT("endpoint")` – PUT request (update data).
- `@DELETE("endpoint")` – DELETE request (delete data).
- `@Query("param")` – parameter passed in the URL (e.g., `?id=1`).
- `@Path("param")` – dynamic parameter in the URL path (e.g., `/products/{id}`).
- `@Body` – send an object as the request body (e.g., in POST).

**API interface example:**
```kotlin
import retrofit2.http.*

interface ApiService {
    @GET("products")
    suspend fun getProducts(): List<Product>

    @GET("product")
    suspend fun getProductById(@Query("id") id: Int): Product

    @POST("products")
    suspend fun addProduct(@Body product: Product): Product

    @PUT("products/{id}")
    suspend fun updateProduct(@Path("id") id: Int, @Body product: Product): Product

    @DELETE("products/{id}")
    suspend fun deleteProduct(@Path("id") id: Int)
}
```

**Explanation:**
- `@GET("products")` – fetches a list of products.
- `@GET("product")` with `@Query("id")` – fetches a product by specific ID (e.g., `/product?id=5`).
- `@POST("products")` with `@Body` – sends a new product to the server.
- `@PUT("products/{id}")` with `@Path` and `@Body` – updates a product by ID.
- `@DELETE("products/{id}")` with `@Path` – deletes a product by ID.

### Returning `Call<T>` in Retrofit Interface

Besides `suspend fun` functions (for coroutines), Retrofit allows you to declare methods that return a `Call<T>` object. This lets you perform asynchronous or synchronous requests without using coroutines.

**Interface example with Call:**
```kotlin
import retrofit2.Call
import retrofit2.http.GET
import retrofit2.http.Query

interface ApiService {
    @GET("products")
    fun getProducts(): Call<List<Product>>

    @GET("product")
    fun getProductById(@Query("id") id: Int): Call<Product>
}
```

**Making a request with Call:**
```kotlin
val call = api.getProducts()
call.enqueue(object : Callback<List<Product>> {
    override fun onResponse(call: Call<List<Product>>, response: Response<List<Product>>) {
        if (response.isSuccessful) {
            val products = response.body()
            // handle data
        }
    }
    override fun onFailure(call: Call<List<Product>>, t: Throwable) {
        // handle error
    }
})
```

**Key features of Call:**
- `Call<T>` lets you manually manage the request (you can cancel, retry, execute synchronously or asynchronously).
- Using `enqueue()` performs the request asynchronously (in the background).
- Using `execute()` performs the request synchronously (blocks the thread—not recommended in UI!).

---

### Creating a Retrofit Instance with Moshi Converter

To use Retrofit in your app, you must create a Retrofit instance and specify:
- **API base URL** (`baseUrl`),
- **JSON converter** (e.g., Moshi or Gson),
- (optional) additional settings, e.g., OkHttp client.

**Example of creating a Retrofit instance with Moshi:**
```kotlin
import retrofit2.Retrofit
import retrofit2.converter.moshi.MoshiConverterFactory
import com.squareup.moshi.Moshi

// Create Moshi instance (add custom adapters if needed)
val moshi = Moshi.Builder().build()

// Create Retrofit instance
val retrofit = Retrofit.Builder()
    .baseUrl("https://your-api.com/api/") // API base URL
    .addConverterFactory(MoshiConverterFactory.create(moshi)) // JSON converter
    .build()

// Create API interface implementation
val api = retrofit.create(ApiService::class.java)
```

**Tips:**
- `baseUrl` must end with a slash `/`.
- You can add a custom OkHttp client, e.g., with interceptors for logging or token handling:
  ```kotlin
  import okhttp3.OkHttpClient
  val client = OkHttpClient.Builder()
      .addInterceptor(MyInterceptor())
      .build()

  val retrofit = Retrofit.Builder()
      .baseUrl("https://your-api.com/api/")
      .addConverterFactory(MoshiConverterFactory.create(moshi))
      .client(client)
      .build()
  ```
- The Retrofit instance should be kept as a singleton (e.g., in an object or Application class) to avoid creating it multiple times.

**Why use Moshi?**
- Moshi is fast, safe, and has great Kotlin support (e.g., nullable types, data classes, annotations).
- Makes it easy to map JSON fields to data model properties.

### Example Usage of Retrofit in ViewModel

Below is an example ViewModel using Retrofit (with Moshi converter) and StateFlow to fetch and observe a product list. The Retrofit instance should be a singleton (e.g., passed via constructor or obtained from an object).

```kotlin
import androidx.lifecycle.ViewModel
import androidx.lifecycle.viewModelScope
import kotlinx.coroutines.flow.MutableStateFlow
import kotlinx.coroutines.flow.StateFlow
import kotlinx.coroutines.launch

class ProductsViewModel(private val api: ApiService) : ViewModel() {

    private val _products = MutableStateFlow<List<Product>>(emptyList())
    val products: StateFlow<List<Product>> = _products

    private val _error = MutableStateFlow<String?>(null)
    val error: StateFlow<String?> = _error

    fun fetchProducts() {
        viewModelScope.launch {
            try {
                _products.value = api.getProducts()
                _error.value = null
            } catch (e: Exception) {
                _error.value = "Error fetching data: ${e.message}"
            }
        }
    }
}
```

**Tips:**
- Pass the `ApiService` instance to the ViewModel via constructor.
- Use a separate StateFlow for error handling.
- Perform all network operations in coroutines (`viewModelScope.launch`) or using `Call`.

---

## Best Practices

- Use `suspend fun` in API interfaces to work with coroutines.
- Handle exceptions (e.g., no internet, server error).
- Keep the Retrofit instance as a singleton.

---

**More information:**  
- [Official Retrofit documentation](https://square.github.io/retrofit/)
- [Retrofit usage examples](https://github.com/square/retrofit/tree/master/samples)
- [Official Moshi documentation](https://github.com/square/moshi)

---

### 🧭 **Next topic:** [Firebase Platform](https://github.com/MarcinRod/AndroidLecture2025EN/blob/main/13%20Firebase%20Platform.md)
