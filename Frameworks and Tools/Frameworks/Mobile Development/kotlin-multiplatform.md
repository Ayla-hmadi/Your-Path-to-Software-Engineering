# Kotlin Multiplatform Mobile

## Introduction
Kotlin Multiplatform Mobile (KMM) is a cross-platform framework that enables code sharing between Android and iOS platforms while maintaining native performance and user experience.

## Core Concepts

### Project Structure
```kotlin
// Shared code in commonMain
kotlin {
    android()
    ios()
    
    sourceSets {
        val commonMain by getting {
            dependencies {
                implementation("org.jetbrains.kotlinx:kotlinx-coroutines-core:1.6.4")
            }
        }
        val androidMain by getting
        val iosMain by getting
    }
}
```

### Shared Logic
1. **Common Code**
   ```kotlin
   // Shared business logic
   expect class Platform {
       val name: String
   }
   
   class Greeting {
       fun greet(): String {
           return "Hello from ${Platform().name}!"
       }
   }
   ```

2. **Platform-Specific Implementation**
   ```kotlin
   // Android
   actual class Platform {
       actual val name: String = "Android"
   }
   
   // iOS
   actual class Platform {
       actual val name: String = "iOS"
   }
   ```

## Data Management

### Repository Pattern
```kotlin
interface UserRepository {
    suspend fun getUsers(): List<User>
    suspend fun getUser(id: String): User
    suspend fun saveUser(user: User)
}

class UserRepositoryImpl(
    private val api: UserApi,
    private val database: UserDatabase
) : UserRepository {
    override suspend fun getUsers(): List<User> {
        return try {
            val users = api.fetchUsers()
            database.saveUsers(users)
            users
        } catch (e: Exception) {
            database.getUsers()
        }
    }
}
```

### Network Requests
```kotlin
class UserApi {
    private val httpClient = HttpClient {
        install(JsonFeature) {
            serializer = KotlinxSerializer()
        }
    }
    
    suspend fun fetchUsers(): List<User> {
        return httpClient.get("https://api.example.com/users")
    }
}
```

## UI Integration

### Platform Views
1. **Android UI**
   ```kotlin
   @Composable
   fun UserList(users: List<User>) {
       LazyColumn {
           items(users) { user ->
               UserCard(user)
           }
       }
   }
   ```

2. **iOS UI (SwiftUI)**
   ```swift
   struct UserList: View {
       let users: [User]
       
       var body: some View {
           List(users) { user in
               UserCard(user: user)
           }
       }
   }
   ```

## State Management

### Shared ViewModel
```kotlin
class UserViewModel : ViewModel() {
    private val _users = MutableStateFlow<List<User>>(emptyList())
    val users: StateFlow<List<User>> = _users.asStateFlow()
    
    fun loadUsers() {
        viewModelScope.launch {
            val result = userRepository.getUsers()
            _users.value = result
        }
    }
}
```

### Platform Integration
```kotlin
// Android
class AndroidUserViewModel(
    private val sharedViewModel: UserViewModel
) : ViewModel() {
    val users = sharedViewModel.users.asLiveData()
}

// iOS
class IOSUserViewModel {
    private let sharedViewModel: UserViewModel
    
    func observeUsers() -> Flow<[User]> {
        return sharedViewModel.users
    }
}
```

## Database Integration

### SQLDelight
```kotlin
CREATE TABLE User (
    id TEXT NOT NULL PRIMARY KEY,
    name TEXT NOT NULL,
    email TEXT NOT NULL
);

selectAll:
SELECT *
FROM User;

insertUser:
INSERT INTO User(id, name, email)
VALUES (?, ?, ?);
```

### Database Access
```kotlin
class DatabaseHelper(private val database: Database) {
    suspend fun getUsers(): List<User> {
        return database.userQueries.selectAll()
            .asFlow()
            .map { it.executeAsList() }
            .first()
    }
    
    suspend fun saveUser(user: User) {
        database.userQueries.insertUser(
            id = user.id,
            name = user.name,
            email = user.email
        )
    }
}
```

## Testing

### Shared Tests
```kotlin
class UserRepositoryTest {
    private lateinit var repository: UserRepository
    private lateinit var api: MockUserApi
    private lateinit var database: MockUserDatabase
    
    @BeforeTest
    fun setup() {
        api = MockUserApi()
        database = MockUserDatabase()
        repository = UserRepositoryImpl(api, database)
    }
    
    @Test
    fun testGetUsers() = runTest {
        val users = repository.getUsers()
        assertTrue(users.isNotEmpty())
    }
}
```

## Dependency Injection

### Koin Integration
```kotlin
val commonModule = module {
    single { UserRepository(get(), get()) }
    single { UserApi() }
    single { DatabaseHelper(get()) }
}

// Platform specific
val androidModule = module {
    single { AndroidUserViewModel(get()) }
}

val iosModule = module {
    single { IOSUserViewModel(get()) }
}
```

## Best Practices

### Code Organization
```text
shared/
├── commonMain/
│   ├── kotlin/
│   │   ├── data/
│   │   ├── domain/
│   │   └── presentation/
├── androidMain/
│   └── kotlin/
└── iosMain/
    └── kotlin/
```

### Error Handling
```kotlin
sealed class Result<out T> {
    data class Success<T>(val data: T) : Result<T>()
    data class Error(val exception: Exception) : Result<Nothing>()
}

suspend fun <T> safeApiCall(
    block: suspend () -> T
): Result<T> = try {
    Result.Success(block())
} catch (e: Exception) {
    Result.Error(e)
}
```

## Conclusion
Kotlin Multiplatform Mobile provides a powerful solution for sharing code between Android and iOS platforms while maintaining native performance and user experience.
