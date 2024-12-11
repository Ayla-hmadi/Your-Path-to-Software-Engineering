# JUnit Testing Guide

## Overview
JUnit is the most widely used testing framework for Java applications. This guide covers JUnit 5 (also known as JUnit Jupiter), which introduces significant improvements over JUnit 4.

## Setup

### Maven Dependencies
```xml
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>5.9.2</version>
    <scope>test</scope>
</dependency>
```

### Gradle Dependencies
```groovy
testImplementation 'org.junit.jupiter:junit-jupiter:5.9.2'
```

## Basic Test Structure

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

public class CalculatorTest {
    @Test
    void addition() {
        Calculator calculator = new Calculator();
        assertEquals(4, calculator.add(2, 2), 
            "2 + 2 should equal 4");
    }
}
```

## JUnit 5 Annotations

### Core Annotations
```java
@Test               // Marks a method as a test case
@DisplayName        // Defines custom display name for test
@Disabled          // Disables a test method or class
@BeforeEach        // Executes before each test method
@AfterEach         // Executes after each test method
@BeforeAll         // Executes once before all test methods
@AfterAll          // Executes once after all test methods
```

### Example with Lifecycle Annotations
```java
public class UserServiceTest {
    private UserService userService;
    private Database database;

    @BeforeAll
    static void initializeTestData() {
        // Set up test data
    }

    @BeforeEach
    void setUp() {
        database = new Database();
        userService = new UserService(database);
    }

    @AfterEach
    void cleanUp() {
        database.clear();
    }

    @AfterAll
    static void tearDown() {
        // Clean up test data
    }
}
```

## Assertions

### Basic Assertions
```java
// Basic assertions
assertEquals(expected, actual);
assertNotEquals(expected, actual);
assertTrue(condition);
assertFalse(condition);
assertNull(object);
assertNotNull(object);

// Array assertions
assertArrayEquals(expectedArray, actualArray);

// Object assertions
assertSame(expected, actual);
assertNotSame(expected, actual);
```

### Advanced Assertions
```java
// Exception testing
assertThrows(Exception.class, () -> {
    // Code that should throw exception
});

// Multiple assertions
assertAll("Person properties",
    () -> assertEquals("John", person.getFirstName()),
    () -> assertEquals("Doe", person.getLastName()),
    () -> assertEquals(25, person.getAge())
);

// Timeout assertions
assertTimeout(Duration.ofSeconds(1), () -> {
    // Code that should complete within 1 second
});
```

## Parameterized Tests

```java
@ParameterizedTest
@ValueSource(strings = {"racecar", "radar", "able was I ere I saw elba"})
void palindromes(String candidate) {
    assertTrue(isPalindrome(candidate));
}

@ParameterizedTest
@CsvSource({
    "1, 1, 2",
    "2, 3, 5",
    "42, 57, 99"
})
void add(int first, int second, int expectedResult) {
    assertEquals(expectedResult, calculator.add(first, second));
}
```

## Test Organization

### Using Nested Tests
```java
@DisplayName("Tests for User class")
class UserTest {
    @Nested
    @DisplayName("when user is new")
    class WhenNew {
        @Test
        void shouldHaveNoPermissions() {
            // Test code
        }
    }

    @Nested
    @DisplayName("when user is admin")
    class WhenAdmin {
        @Test
        void shouldHaveAllPermissions() {
            // Test code
        }
    }
}
```

### Using Tags
```java
@Tag("integration")
class DatabaseTest {
    @Test
    @Tag("slow")
    void testLargeDataSet() {
        // Test code
    }
}
```

## Best Practices

1. **Naming Conventions**
   - Use descriptive test names
   - Follow pattern: `methodName_stateUnderTest_expectedBehavior`
   ```java
   @Test
   void divide_byZero_throwsException() {
       // Test code
   }
   ```

2. **Test Independence**
   - Each test should run independently
   - Clean up resources after tests
   - Don't rely on test execution order

3. **FIRST Principles**
   - Fast: Tests should run quickly
   - Independent: Tests shouldn't depend on each other
   - Repeatable: Tests should be consistent
   - Self-validating: Tests should have boolean output
   - Timely: Tests should be written before or with code

## Mocking with Mockito

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {
    @Mock
    private UserRepository userRepository;

    @InjectMocks
    private UserService userService;

    @Test
    void shouldReturnUser() {
        when(userRepository.findById(1L))
            .thenReturn(Optional.of(new User(1L, "John")));

        User user = userService.getUser(1L);
        assertEquals("John", user.getName());
    }
}
```

## Test Coverage

### Using JaCoCo
```xml
<plugin>
    <groupId>org.jacoco</groupId>
    <artifactId>jacoco-maven-plugin</artifactId>
    <version>0.8.8</version>
    <executions>
        <execution>
            <goals>
                <goal>prepare-agent</goal>
            </goals>
        </execution>
        <execution>
            <id>report</id>
            <phase>test</phase>
            <goals>
                <goal>report</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

## Resources

- [JUnit 5 User Guide](https://junit.org/junit5/docs/current/user-guide/)
- [Mockito Documentation](https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html)
- [JaCoCo Documentation](https://www.jacoco.org/jacoco/trunk/doc/)
