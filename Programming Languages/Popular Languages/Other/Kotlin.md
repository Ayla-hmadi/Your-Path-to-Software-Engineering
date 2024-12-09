# Kotlin Programming Language

## Introduction
Kotlin is a modern, cross-platform programming language that runs on the JVM and can be compiled to JavaScript or native code. It's officially supported for Android development and offers seamless interoperability with Java.

## Core Features

### Null Safety
1. **Nullable Types**
   ```kotlin
   // Nullable vs Non-nullable
   var nonNull: String = "Hello"
   var nullable: String? = null
   
   // Safe calls
   val length = nullable?.length
   
   // Elvis operator
   val len = nullable?.length ?: 0
   
   // Not-null assertion
   val definitelyNotNull = nullable!!
   ```

2. **Smart Casts**
   ```kotlin
   fun processValue(value: Any) {
       if (value is String) {
           // Automatically cast to String
           println(value.length)
       } else if (value is Int) {
           // Automatically cast to Int
           println(value + 1)
       }
   }
   ```

### Functions
1. **Function Declarations**
   ```kotlin
   // Basic function
   fun greet(name: String): String {
       return "Hello, $name!"
   }
   
   // Single-expression function
   fun add(a: Int, b: Int) = a + b
   
   // Default parameters
   fun repeat(str: String, times: Int = 1) = str.repeat(times)
   ```

2. **Extension Functions**
   ```kotlin
   fun String.addExclamation() = "$this!"
   
   fun List<Int>.average(): Double {
       return if (isEmpty()) 0.0 else sum().toDouble() / size
   }
   
   // Usage
   val message = "Hello".addExclamation()
   val avg = listOf(1, 2, 3).average()
   ```

### Data Classes
```kotlin
data class User(
    val id: Int,
    val name: String,
    var email: String
) {
    fun validateEmail(): Boolean {
        return email.contains("@")
    }
}

// Usage
val user = User(1, "John", "john@example.com")
val copy = user.copy(email = "new@example.com")
val (id, name, _) = user  // Destructuring
```

### Coroutines
1. **Basic Coroutines**
   ```kotlin
   import kotlinx.coroutines.*

   suspend fun fetchUser(): User {
       delay(1000)  // Non-blocking delay
       return User(1, "John", "john@example.com")
   }

   fun main() = runBlocking {
       val user = fetchUser()
       println(user)
       
       // Parallel execution
       val users = (1..3).map { id ->
           async { fetchUser() }
       }.awaitAll()
   }
   ```

2. **Flow**
   ```kotlin
   fun numbers(): Flow<Int> = flow {
       for (i in 1..3) {
           delay(100)
           emit(i)
       }
   }

   suspend fun collectNumbers() {
       numbers().collect { value ->
           println(value)
       }
   }
   ```

### Collections
1. **Collection Operations**
   ```kotlin
   val numbers = listOf(1, 2, 3, 4, 5)
   
   // Transformations
   val doubled = numbers.map { it * 2 }
   val filtered = numbers.filter { it % 2 == 0 }
   
   // Reductions
   val sum = numbers.reduce { acc, value -> acc + value }
   val folded = numbers.fold(0) { acc, value -> acc + value }
   ```

2. **Sequence Operations**
   ```kotlin
   val sequence = generateSequence(1) { it + 1 }
       .take(5)
       .filter { it % 2 == 0 }
       .map { it * it }
       .toList()
   ```

## Android Development

### Android Components
```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // View Binding
        val binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)
        
        binding.button.setOnClickListener {
            // Handle click
        }
    }
}
```

### ViewModel
```kotlin
class UserViewModel : ViewModel() {
    private val _users = MutableLiveData<List<User>>()
    val users: LiveData<List<User>> = _users
    
    fun loadUsers() {
        viewModelScope.launch {
            val result = repository.getUsers()
            _users.value = result
        }
    }
}
```

## Testing

### Unit Testing
```kotlin
class CalculatorTest {
    @Test
    fun `adding two numbers returns their sum`() {
        val calculator = Calculator()
        assertEquals(4, calculator.add(2, 2))
    }
    
    @Test
    fun `dividing by zero throws exception`() {
        val calculator = Calculator()
        assertThrows<IllegalArgumentException> {
            calculator.divide(1, 0)
        }
    }
}
```

### Coroutine Testing
```kotlin
class UserServiceTest {
    @Test
    fun `fetchUser returns user data`() = runTest {
        val service = UserService()
        val user = service.fetchUser(1)
        assertNotNull(user)
    }
}
```

## Interoperability with Java

### Java Interop
```kotlin
// Kotlin code using Java class
val javaList = ArrayList<String>()
javaList.add("Kotlin")
javaList.add("Java")

// Java annotations
@JvmField
val constant = "Value"

@JvmStatic
fun staticMethod() {
    // Method accessible as static in Java
}
```

## Best Practices

### Coding Conventions
```kotlin
// Object declaration
object Singleton {
    fun doSomething() {}
}

// Companion object
class MyClass {
    companion object {
        const val CONSTANT = "value"
    }
}

// Extension function placement
fun String.validate() = isNotEmpty() && length > 3
```

### Scope Functions
```kotlin
// let for nullable objects
nullable?.let {
    // Use 'it' safely
}

// with for grouping operations
with(user) {
    name = "John"
    email = "john@example.com"
}

// apply for object configuration
val user = User().apply {
    name = "John"
    email = "john@example.com"
}
```

## Conclusion
Kotlin combines modern language features with pragmatic design choices, making it an excellent choice for Android development and server-side applications while maintaining seamless Java interoperability.
