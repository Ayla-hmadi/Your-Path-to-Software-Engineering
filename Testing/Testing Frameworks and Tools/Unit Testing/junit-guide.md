# JUnit Testing Framework

## Overview

### Introduction
```yaml
Description:
  Industry standard testing framework for Java applications

Key Features:
  - Annotations based
  - Assertions library
  - Parameterized tests
  - Test lifecycle
  - Test suites
  - Parallel execution
```

### Setup
```xml
<!-- Maven Configuration -->
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>5.9.2</version>
    <scope>test</scope>
</dependency>

<!-- Gradle Configuration -->
testImplementation 'org.junit.jupiter:junit-jupiter:5.9.2'
```

## Basic Testing

### Test Structure
```java
// Basic test class
public class CalculatorTest {
    @Test
    void addition() {
        Calculator calc = new Calculator();
        assertEquals(4, calc.add(2, 2), 
            "2 + 2 should equal 4");
    }
}

// Test suite
@DisplayName("Calculator Tests")
class CalculatorTests {
    private Calculator calculator;

    @BeforeEach
    void setup() {
        calculator = new Calculator();
    }

    @Test
    @DisplayName("Addition test")
    void testAdd() {
        assertEquals(4, calculator.add(2, 2));
    }
}
```

### Assertions
```java
// Basic assertions
assertEquals(expected, actual);
assertNotEquals(unexpected, actual);
assertTrue(condition);
assertFalse(condition);
assertNull(object);
assertNotNull(object);

// Advanced assertions
assertAll("group",
    () -> assertEquals(2, 1 + 1),
    () -> assertTrue(4 > 3)
);

// Exception testing
assertThrows(Exception.class, 
    () -> methodThatThrows());
```

## Advanced Features

### Parameterized Tests
```java
@ParameterizedTest
@ValueSource(strings = {"radar", "level"})
void palindromes(String input) {
    assertTrue(isPalindrome(input));
}

@ParameterizedTest
@CsvSource({
    "1, 1, 2",
    "2, 3, 5",
    "42, 57, 99"
})
void addNumbers(int first, int second, int expected) {
    assertEquals(expected, calculator.add(first, second));
}
```

### Dynamic Tests
```java
@TestFactory
Collection<DynamicTest> dynamicTests() {
    return Arrays.asList(
        dynamicTest("simple test",
            () -> assertTrue(true)),
        dynamicTest("exception test",
            () -> assertThrows(Exception.class,
                () -> { throw new Exception(); }))
    );
}
```

## Test Lifecycle

### Lifecycle Annotations
```java
class LifecycleTest {
    @BeforeAll
    static void initAll() {
        // Run once before all tests
    }

    @BeforeEach
    void init() {
        // Run before each test
    }

    @AfterEach
    void tearDown() {
        // Run after each test
    }

    @AfterAll
    static void tearDownAll() {
        // Run once after all tests
    }
}
```

### Test Instance Lifecycle
```java
@TestInstance(TestInstance.Lifecycle.PER_CLASS)
class SharedInstanceTest {
    private List<String> items;

    @BeforeAll
    void setup() {
        items = new ArrayList<>();
    }

    @Test
    void testSharedState() {
        items.add("item");
        assertFalse(items.isEmpty());
    }
}
```

## Test Organization

### Test Suites
```java
@Suite
@SelectPackages("com.example.tests")
@IncludeTagsValue("integration")
class TestSuite {
}

@Suite.SuiteClasses({
    UserTest.class,
    OrderTest.class,
    ProductTest.class
})
class AllTests {
}
```

### Tags and Filtering
```java
@Tag("fast")
@Test
void fastTest() {
    // Fast test implementation
}

@Tag("slow")
@Test
void slowTest() {
    // Slow test implementation
}
```

## Advanced Testing

### Timeout Testing
```java
@Test
@Timeout(value = 100, unit = TimeUnit.MILLISECONDS)
void timeoutTest() {
    // Test must complete within 100ms
}

@Test
void timeoutAssertion() {
    assertTimeout(Duration.ofMillis(100), () -> {
        // Code that should complete within 100ms
    });
}
```

### Assumptions
```java
@Test
void testOnlyOnDevelopmentMachine() {
    assumeTrue("DEV".equals(System.getenv("ENV")));
    // Test code here
}

@Test
void testOnlyOnSpecificOS() {
    assumingThat(System.getProperty("os.name").startsWith("Windows"),
        () -> {
            // Windows-specific test code
        });
}
```

## Extension Model

### Custom Extensions
```java
class LoggingExtension implements 
    BeforeEachCallback, AfterEachCallback {
    
    @Override
    public void beforeEach(ExtensionContext context) {
        System.out.println("Before test: " + 
            context.getDisplayName());
    }

    @Override
    public void afterEach(ExtensionContext context) {
        System.out.println("After test: " + 
            context.getDisplayName());
    }
}

@ExtendWith(LoggingExtension.class)
class MyTest {
    // Test methods
}
```

### Built-in Extensions
```java
// Temporary folder
@ExtendWith(TempDirectory.class)
class FileTest {
    @Test
    void testWithTempDir(@TempDir Path tempDir) {
        // Use temporary directory
    }
}
```

## Best Practices

### Test Structure
```yaml
Best Practices:
  - One assertion per test
  - Clear test names
  - Proper setup/teardown
  - Independent tests
  - Focused scope

Organization:
  - Logical test grouping
  - Consistent naming
  - Clear structure
  - Documented tests
```

### Test Naming
```java
@Test
void should_AddTwoNumbers_When_InputIsValid() {
    // Test implementation
}

@Test
void should_ThrowException_When_InputIsInvalid() {
    // Test implementation
}
```
